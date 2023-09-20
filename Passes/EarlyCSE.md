## EarlyCSE

公共子表达式合并
目前并未发现，但考虑合并GEP时仅有一个GEP带有TBAA的情况？

### 删除GEP的情况

1. Instruction Trivially Dead(The result produced by the instruction is not used, and the instruction has no side effects)(极少，十数条指令)
2. Indices均为0(1万余条指令)
3. 结果已被计算过(2万余条指令)

情况1：

```
# IR Dump After SROAPass on d_demangle
; Function Attrs: nounwind uwtable
define internal fastcc ptr @d_demangle(ptr noundef %0, i32 noundef %1, ptr nocapture noundef writeonly %2) unnamed_addr #5 [4] {
  %4 = alloca %struct.d_growable_string, align 8
  call void @llvm.lifetime.start.p0(i64 32, ptr nonnull %4) #23
  %5 = getelementptr inbounds %struct.d_growable_string, ptr %4, i64 0, i32 2, !tbaa !110
  %6 = getelementptr inbounds %struct.d_growable_string, ptr %4, i64 0, i32 3, !tbaa !112
  call void @llvm.memset.p0.i64(ptr noundef nonnull align 8 dereferenceable(28) %4, i8 0, i64 28, i1 false)
  %7 = call fastcc i32 @d_demangle_callback(ptr noundef %0, i32 noundef %1, ptr noundef nonnull @d_growable_string_callback_adapter, ptr noundef nonnull %4)
  %8 = icmp eq i32 %7, 0
  br i1 %8, label %9, label %11
%5指针在函数中无使用。
```



情况2：

```
# IR Dump After SROAPass on xre_set_registers
; Function Attrs: nounwind uwtable
define dso_local void @xre_set_registers(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, ptr noundef %4) #0 [8] {
  %6 = icmp ne i32 %2, 0
  br i1 %6, label %7, label %15

7:                                                ; preds = %5
  %8 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i32 0, i32 7
  %9 = load i8, ptr %8, align 8
  %10 = and i8 %9, -7
  %11 = or i8 %10, 2
  store i8 %11, ptr %8, align 8
  %12 = getelementptr inbounds %struct.re_registers, ptr %1, i32 0, i32 0, !tbaa !27
  store i32 %2, ptr %12, align 8, !tbaa !27
  %13 = getelementptr inbounds %struct.re_registers, ptr %1, i32 0, i32 1, !tbaa !29
  store ptr %3, ptr %13, align 8, !tbaa !29
  %14 = getelementptr inbounds %struct.re_registers, ptr %1, i32 0, i32 2, !tbaa !30
  store ptr %4, ptr %14, align 8, !tbaa !30
  br label %23
---------------------------------
# IR Dump After EarlyCSEPass on xre_set_registers
; Function Attrs: nounwind uwtable
define dso_local void @xre_set_registers(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, ptr noundef %4) #0 [6] {
  %6 = icmp ne i32 %2, 0
  br i1 %6, label %7, label %14

7:                                                ; preds = %5
  %8 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i32 0, i32 7
  %9 = load i8, ptr %8, align 8
  %10 = and i8 %9, -7
  %11 = or i8 %10, 2
  store i8 %11, ptr %8, align 8
  store i32 %2, ptr %1, align 8, !tbaa !27
  %12 = getelementptr inbounds %struct.re_registers, ptr %1, i32 0, i32 1, !tbaa !29
  store ptr %3, ptr %12, align 8, !tbaa !29
  %13 = getelementptr inbounds %struct.re_registers, ptr %1, i32 0, i32 2, !tbaa !30
  store ptr %4, ptr %13, align 8, !tbaa !30
  br label %20
%8的偏移量均为0,所以被删除。
```

情况3：

```
# IR Dump Before EarlyCSEPass on d_demangle_callback
; Function Attrs: nounwind uwtable
define internal i32 @d_demangle_callback(ptr noundef %0, i32 noundef %1, ptr noundef %2, ptr noundef %3) #0 [21] !dbg !404 {
.......
61:                                               ; preds = %49, %60, %15
  %62 = phi i32 [ 1, %15 ], [ %55, %49 ], [ 0, %60 ], !dbg !2671
  call void @llvm.dbg.value(metadata i32 %62, metadata !422, metadata !DIExpression()), !dbg !2629
  %63 = call i64 @strlen(ptr noundef %0) #13, !dbg !2672
  call void @cplus_demangle_init_info(ptr noundef %0, i32 noundef %1, i64 noundef %63, ptr noundef %5), !dbg !2673
  %64 = and i32 %1, 262144, !dbg !2674
  %65 = icmp eq i32 %64, 0, !dbg !2676
  %66 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 6
  %67 = load i32, ptr %66, align 4
  %68 = sext i32 %67 to i64
  %69 = icmp ugt i64 %68, 2048
  %70 = select i1 %65, i1 %69, i1 false, !dbg !2677
  br i1 %70, label %71, label %72, !dbg !2677

71:                                               ; preds = %61
  br label %124, !dbg !2678

72:                                               ; preds = %61
  %73 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 6, !dbg !2680, !tbaa !2150
```

### 情况3存在问题

```
# IR Dump After SROAPass on d_demangle_callback ***
; Function Attrs: nounwind uwtable
define internal i32 @d_demangle_callback(ptr noundef %0, i32 noundef %1, ptr noundef %2, ptr noundef %3) #0 [21] !dbg !404 {
61:                                               ; preds = %49, %60, %15
  %62 = phi i32 [ 1, %15 ], [ %55, %49 ], [ 0, %60 ], !dbg !2671
  call void @llvm.dbg.value(metadata i32 %62, metadata !422, metadata !DIExpression()), !dbg !2629
  %63 = call i64 @strlen(ptr noundef %0) #13, !dbg !2672
  call void @cplus_demangle_init_info(ptr noundef %0, i32 noundef %1, i64 noundef %63, ptr noundef %5), !dbg !2673
  %64 = and i32 %1, 262144, !dbg !2674
  %65 = icmp eq i32 %64, 0, !dbg !2676
  %66 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 6
  %67 = load i32, ptr %66, align 4
  %68 = sext i32 %67 to i64
  %69 = icmp ugt i64 %68, 2048
  %70 = select i1 %65, i1 %69, i1 false, !dbg !2677
  br i1 %70, label %71, label %72, !dbg !2677

71:                                               ; preds = %61
  br label %124, !dbg !2678

72:                                               ; preds = %61
  %73 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 6, !dbg !2680, !tbaa !2150
  %74 = load i32, ptr %73, align 4, !dbg !2680, !tbaa !2150
  %75 = zext i32 %74 to i64, !dbg !2681
  %76 = call ptr @llvm.stacksave(), !dbg !2681
  %77 = alloca %struct.demangle_component, i64 %75, align 16, !dbg !2681
  call void @llvm.dbg.value(metadata i64 %75, metadata !529, metadata !DIExpression()), !dbg !2682
  call void @llvm.dbg.declare(metadata ptr %77, metadata !531, metadata !DIExpression()), !dbg !2683
  %78 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 9, !dbg !2684, !tbaa !1716
  %79 = load i32, ptr %78, align 4, !dbg !2684, !tbaa !1716
  %80 = zext i32 %79 to i64, !dbg !2685
  %81 = alloca ptr, i64 %80, align 16, !dbg !2685
  call void @llvm.dbg.value(metadata i64 %80, metadata !535, metadata !DIExpression()), !dbg !2682
  call void @llvm.dbg.declare(metadata ptr %81, metadata !536, metadata !DIExpression()), !dbg !2686
  %82 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 4, !dbg !2687, !tbaa !2155
  store ptr %77, ptr %82, align 8, !dbg !2688, !tbaa !2155
  %83 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 7, !dbg !2689, !tbaa !1721
  store ptr %81, ptr %83, align 8, !dbg !2690, !tbaa !1721
  switch i32 %62, label %105 [
    i32 0, label %84
    i32 1, label %86
    i32 2, label %88
    i32 3, label %88
  ], !dbg !2691
}
[EarlyCSE]Processing instruction:  %72 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 6, !dbg !921, !tbaa !922
[EarlyCSE]Formal instruction:  %65 = getelementptr inbounds %struct.d_info, ptr %5, i32 0, i32 6
```




```
if (SimpleValue::canHandle(&Inst)) {
    // See if the instruction has an available value.  If so, use it.
    if (Value *V = AvailableValues.lookup(&Inst)) {
    LLVM_DEBUG(dbgs() << "EarlyCSE CSE: " << Inst << "  to: " << *V
                      << '\n');

    if (!DebugCounter::shouldExecute(CSECounter)) {
      LLVM_DEBUG(dbgs() << "Skipping due to debug counter\n");
      continue;
    }
    if (auto *I = dyn_cast<Instruction>(V)) {
      // If I being poison triggers UB, there is no need to drop those
      // flags. Otherwise, only retain flags present on both I and Inst.
      // TODO: Currently some fast-math flags are not treated as
      // poison-generating even though they should. Until this is fixed,
      // always retain flags present on both I and Inst for floating point
      // instructions.
      if (isa<FPMathOperator>(I) ||
          (I->hasPoisonGeneratingFlags() && !programUndefinedIfPoison(I)))
        I->andIRFlags(&Inst);
    }
    // ADDZY Check TBAA
    if (auto *GEP = dyn_cast<GetElementPtrInst>(V)) {
      llvm::MDNode *N1 = Inst.getMetadata(LLVMContext::MD_tbaa);
      llvm::MDNode *N2 = GEP->getMetadata(LLVMContext::MD_tbaa);
      if (!compareMDNodes(N1, N2)) {
        llvm::errs() << "[EarlyCSE]Processing instruction:" << Inst << "\n";
        llvm::errs() << "[EarlyCSE]Formal instruction:" << *V << "\n";
      }
    }
    Inst.replaceAllUsesWith(V);
    salvageKnowledge(&Inst, &AC);
    removeMSSA(Inst);
    Inst.eraseFromParent();
    Changed = true;
    ++NumCSE;
    continue;
    }

    // Otherwise, just remember that this value is available.
    AvailableValues.insert(&Inst, &Inst);
    continue;
}
```

