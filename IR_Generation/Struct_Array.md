## 结构体数组TBAA缺失问题

源码：

```c++
#include <stdio.h>
struct complex {
    float real;
    int imag;
};
struct number {
    struct complex comp;
    int integer;
} ;
__attribute__ ((__noinline__, __disable_tail_calls__))
void load_test(int *ptr)
{
    printf("Before %d\n",*ptr);
    (*ptr) = 10;
    printf("Now how to load %d\n",(*ptr));
}
int main() {
    struct number num[3] = {
        {{0.0, 10}, 20},
        {{0.0, 11}, 21},
        {{0.0, 12}, 23}
    };
    struct number *num1 = &num[1];
    load_test(&(num1->integer));
    return 0;
}
```




生成的IR：

```plain
; ModuleID = 'case5.c'

%struct.number = type { %struct.complex, i32 }
%struct.complex = type { float, i32 }

@.str = private unnamed_addr constant [11 x i8] c"Before %d\0A\00", align 1, !dbg !0
@.str.1 = private unnamed_addr constant [20 x i8] c"Now how to load %d\0A\00", align 1, !dbg !7

; Function Attrs: noinline nounwind uwtable
define dso_local void @load_test(ptr noundef %0) #0 !dbg !21 {
  %2 = alloca ptr, align 8
  store ptr %0, ptr %2, align 8, !tbaa !28
  call void @llvm.dbg.declare(metadata ptr %2, metadata !27, metadata !DIExpression()), !dbg !32
  %3 = load ptr, ptr %2, align 8, !dbg !33, !tbaa !28
  %4 = load i32, ptr %3, align 4, !dbg !34, !tbaa !35
  %5 = call i32 (ptr, ...) @printf(ptr noundef @.str, i32 noundef %4), !dbg !37
  %6 = load ptr, ptr %2, align 8, !dbg !38, !tbaa !28
  store i32 10, ptr %6, align 4, !dbg !39, !tbaa !35
  %7 = load ptr, ptr %2, align 8, !dbg !40, !tbaa !28
  %8 = load i32, ptr %7, align 4, !dbg !41, !tbaa !35
  %9 = call i32 (ptr, ...) @printf(ptr noundef @.str.1, i32 noundef %8), !dbg !42
  ret void, !dbg !43
}

; Function Attrs: nounwind uwtable
define dso_local i32 @main() #3 !dbg !44 {
  %1 = alloca i32, align 4
  %2 = alloca [3 x %struct.number], align 16
  %3 = alloca ptr, align 8
  store i32 0, ptr %1, align 4
  call void @llvm.lifetime.start.p0(i64 36, ptr %2) #6, !dbg !63
  call void @llvm.dbg.declare(metadata ptr %2, metadata !48, metadata !DIExpression()), !dbg !64
  call void @llvm.memset.p0.i64(ptr align 16 %2, i8 0, i64 36, i1 false), !dbg !64
  %4 = getelementptr inbounds [3 x %struct.number], ptr %2, i32 0, i32 0, !dbg !64
  %5 = getelementptr inbounds %struct.number, ptr %4, i32 0, i32 0, !dbg !64
  %6 = getelementptr inbounds %struct.complex, ptr %5, i32 0, i32 1, !dbg !64
  store i32 10, ptr %6, align 4, !dbg !64
  %7 = getelementptr inbounds %struct.number, ptr %4, i32 0, i32 1, !dbg !64
  store i32 20, ptr %7, align 8, !dbg !64
  %8 = getelementptr inbounds [3 x %struct.number], ptr %2, i32 0, i32 1, !dbg !64
  %9 = getelementptr inbounds %struct.number, ptr %8, i32 0, i32 0, !dbg !64
  %10 = getelementptr inbounds %struct.complex, ptr %9, i32 0, i32 1, !dbg !64
  store i32 11, ptr %10, align 4, !dbg !64
  %11 = getelementptr inbounds %struct.number, ptr %8, i32 0, i32 1, !dbg !64
  store i32 21, ptr %11, align 4, !dbg !64
  %12 = getelementptr inbounds [3 x %struct.number], ptr %2, i32 0, i32 2, !dbg !64
  %13 = getelementptr inbounds %struct.number, ptr %12, i32 0, i32 0, !dbg !64
  %14 = getelementptr inbounds %struct.complex, ptr %13, i32 0, i32 1, !dbg !64
  store i32 12, ptr %14, align 4, !dbg !64
  %15 = getelementptr inbounds %struct.number, ptr %12, i32 0, i32 1, !dbg !64
  store i32 23, ptr %15, align 8, !dbg !64
  call void @llvm.lifetime.start.p0(i64 8, ptr %3) #6, !dbg !65
  call void @llvm.dbg.declare(metadata ptr %3, metadata !61, metadata !DIExpression()), !dbg !66
  %16 = getelementptr inbounds [3 x %struct.number], ptr %2, i64 0, i64 1, !dbg !67
  store ptr %16, ptr %3, align 8, !dbg !66, !tbaa !28
  %17 = load ptr, ptr %3, align 8, !dbg !68, !tbaa !28
  %18 = getelementptr inbounds %struct.number, ptr %17, i32 0, i32 1, !dbg !69, !tbaa !70
  call void @load_test(ptr noundef %18), !dbg !74
  call void @llvm.lifetime.end.p0(i64 8, ptr %3) #6, !dbg !75
  call void @llvm.lifetime.end.p0(i64 36, ptr %2) #6, !dbg !75
  ret i32 0, !dbg !76
}

!28 = !{!29, !29, i64 0}
!29 = !{!"any pointer", !30, i64 0}
!30 = !{!"omnipotent char", !31, i64 0}
!31 = !{!"Simple C/C++ TBAA"}
!35 = !{!36, !36, i64 0}
!36 = !{!"int", !30, i64 0}
!70 = !{!71, !36, i64 8}
!71 = !{!"number", !72, i64 0, !36, i64 8}
!72 = !{!"complex", !73, i64 0, !36, i64 4}
!73 = !{!"float", !30, i64 0}
```


相关输出信息：

```
[CreateConstArrayGEP]: @.str = private unnamed_addr constant [11 x i8] c"Before %d\0A\00", align 1, !dbg !0
[CreateConstArrayGEP]: @.str.1 = private unnamed_addr constant [20 x i8] c"Now how to load %d\0A\00", align 1, !dbg !7

[CreateConstInBoundsGEP2_32]:   %4 = getelementptr inbounds [3 x %struct.number], ptr %2, i32 0, i32 0, !dbg !32
[CreateConstInBoundsGEP2_32]:   %5 = getelementptr inbounds %struct.number, ptr %4, i32 0, i32 0, !dbg !32
[CreateConstInBoundsGEP2_32]:   %6 = getelementptr inbounds %struct.complex, ptr %5, i32 0, i32 1, !dbg !32
[CreateConstInBoundsGEP2_32]:   %7 = getelementptr inbounds %struct.number, ptr %4, i32 0, i32 1, !dbg !32
[CreateConstInBoundsGEP2_32]:   %8 = getelementptr inbounds [3 x %struct.number], ptr %2, i32 0, i32 1, !dbg !32
[CreateConstInBoundsGEP2_32]:   %9 = getelementptr inbounds %struct.number, ptr %8, i32 0, i32 0, !dbg !32
[CreateConstInBoundsGEP2_32]:   %10 = getelementptr inbounds %struct.complex, ptr %9, i32 0, i32 1, !dbg !32
[CreateConstInBoundsGEP2_32]:   %11 = getelementptr inbounds %struct.number, ptr %8, i32 0, i32 1, !dbg !32
[CreateConstInBoundsGEP2_32]:   %12 = getelementptr inbounds [3 x %struct.number], ptr %2, i32 0, i32 2, !dbg !32
[CreateConstInBoundsGEP2_32]:   %13 = getelementptr inbounds %struct.number, ptr %12, i32 0, i32 0, !dbg !32
[CreateConstInBoundsGEP2_32]:   %14 = getelementptr inbounds %struct.complex, ptr %13, i32 0, i32 1, !dbg !32
[CreateConstInBoundsGEP2_32]:   %15 = getelementptr inbounds %struct.number, ptr %12, i32 0, i32 1, !dbg !32
```

问题总结：
基本元素为嵌套结构体的数组中，访问内层结构体的字段时因为对数组GEP的指令不存在TBAA，