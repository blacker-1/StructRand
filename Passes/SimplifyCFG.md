

## SimplifyCFG

```
*** IR Dump After InstCombinePass on splay_tree_splay ***
; Function Attrs: nounwind uwtable
define internal fastcc void @splay_tree_splay(ptr noundef %0, i64 noundef %1) unnamed_addr #0 [13] {
  %3 = load ptr, ptr %0, align 8, !tbaa !5
  %4 = icmp eq ptr %3, null
  br i1 %4, label %77, label %5

5:                                                ; preds = %75, %2
  %6 = load ptr, ptr %0, align 8, !tbaa !5
  %7 = getelementptr inbounds %struct.splay_tree_s, ptr %0, i64 0, i32 1, !tbaa !10
  %8 = load ptr, ptr %7, align 8, !tbaa !10
  %9 = load i64, ptr %6, align 8, !tbaa !16
  %10 = call i32 %8(i64 noundef %1, i64 noundef %9) #5
  %11 = icmp eq i32 %10, 0
  br i1 %11, label %12, label %13

12:                                               ; preds = %5
  br label %75

13:                                               ; preds = %5
  %14 = icmp slt i32 %10, 0
  br i1 %14, label %15, label %17

15:                                               ; preds = %13
  %16 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 2, !tbaa !23
  br label %19

17:                                               ; preds = %13
  %18 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 3, !tbaa !24
  br label %19

19:                                               ; preds = %17, %15
  %20 = phi ptr [ %16, %15 ], [ %18, %17 ]
  %21 = load ptr, ptr %20, align 8, !tbaa !26
  %22 = icmp eq ptr %21, null
  br i1 %22, label %23, label %24

23:                                               ; preds = %19
  br label %75

24:                                               ; preds = %19
  %25 = load ptr, ptr %7, align 8, !tbaa !10
  %26 = load i64, ptr %21, align 8, !tbaa !16
  %27 = call i32 %25(i64 noundef %1, i64 noundef %26) #5
  %28 = icmp eq i32 %27, 0
  br i1 %28, label %41, label %29

29:                                               ; preds = %24
  %30 = icmp slt i32 %27, 0
  br i1 %30, label %31, label %35

31:                                               ; preds = %29
  %32 = getelementptr inbounds %struct.splay_tree_node_s, ptr %21, i64 0, i32 2, !tbaa !23
  %33 = load ptr, ptr %32, align 8, !tbaa !23
  %34 = icmp eq ptr %33, null
  br i1 %34, label %41, label %35

35:                                               ; preds = %31, %29
  %36 = icmp sgt i32 %27, 0
  br i1 %36, label %37, label %45

37:                                               ; preds = %35
  %38 = getelementptr inbounds %struct.splay_tree_node_s, ptr %21, i64 0, i32 3, !tbaa !24
  %39 = load ptr, ptr %38, align 8, !tbaa !24
  %40 = icmp eq ptr %39, null
  br i1 %40, label %41, label %45

41:                                               ; preds = %37, %31, %24
  br i1 %14, label %42, label %43

42:                                               ; preds = %41
  call fastcc void @rotate_left(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef nonnull %21)
  br label %44

43:                                               ; preds = %41
  call fastcc void @rotate_right(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef nonnull %21)
  br label %44

44:                                               ; preds = %43, %42
  br label %75

45:                                               ; preds = %37, %35
  %46 = select i1 %14, i1 %30, i1 false
  br i1 %46, label %47, label %52

47:                                               ; preds = %45
  %48 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 2, !tbaa !23
  %49 = getelementptr inbounds %struct.splay_tree_node_s, ptr %21, i64 0, i32 2, !tbaa !23
  %50 = load ptr, ptr %49, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %48, ptr noundef nonnull %21, ptr noundef %50)
  %51 = load ptr, ptr %48, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %51)
  br label %74

52:                                               ; preds = %45
  %53 = icmp sgt i32 %10, 0
  %54 = select i1 %53, i1 %36, i1 false
  br i1 %54, label %55, label %60

55:                                               ; preds = %52
  %56 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 3, !tbaa !24
  %57 = getelementptr inbounds %struct.splay_tree_node_s, ptr %21, i64 0, i32 3, !tbaa !24
  %58 = load ptr, ptr %57, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %56, ptr noundef nonnull %21, ptr noundef %58)
  %59 = load ptr, ptr %56, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %59)
  br label %74

60:                                               ; preds = %52
  %61 = select i1 %14, i1 %36, i1 false
  br i1 %61, label %62, label %67

62:                                               ; preds = %60
  %63 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 2, !tbaa !23
  %64 = getelementptr inbounds %struct.splay_tree_node_s, ptr %21, i64 0, i32 3, !tbaa !24
  %65 = load ptr, ptr %64, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %63, ptr noundef nonnull %21, ptr noundef %65)
  %66 = load ptr, ptr %63, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %66)
  br label %74

67:                                               ; preds = %60
  %68 = select i1 %53, i1 %30, i1 false
  br i1 %68, label %69, label %74

69:                                               ; preds = %67
  %70 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 3, !tbaa !24
  %71 = getelementptr inbounds %struct.splay_tree_node_s, ptr %21, i64 0, i32 2, !tbaa !23
  %72 = load ptr, ptr %71, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %70, ptr noundef nonnull %21, ptr noundef %72)
  %73 = load ptr, ptr %70, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %73)
  br label %74

74:                                               ; preds = %55, %67, %69, %62, %47
  br label %75

75:                                               ; preds = %74, %44, %23, %12
  %76 = phi i1 [ true, %12 ], [ true, %44 ], [ false, %74 ], [ true, %23 ]
  switch i1 %76, label %78 [
    i1 false, label %5
    i1 true, label %77
  ], !llvm.loop !27

77:                                               ; preds = %2, %75
  ret void

78:                                               ; preds = %75
  unreachable
}
*** IR Dump After SimplifyCFGPass on splay_tree_splay ***
; Function Attrs: nounwind uwtable
define internal fastcc void @splay_tree_splay(ptr noundef %0, i64 noundef %1) unnamed_addr #0 [13] {
  %3 = load ptr, ptr %0, align 8, !tbaa !5
  %4 = icmp eq ptr %3, null
  br i1 %4, label %71, label %5

5:                                                ; preds = %68, %2
  %6 = load ptr, ptr %0, align 8, !tbaa !5
  %7 = getelementptr inbounds %struct.splay_tree_s, ptr %0, i64 0, i32 1, !tbaa !10
  %8 = load ptr, ptr %7, align 8, !tbaa !10
  %9 = load i64, ptr %6, align 8, !tbaa !16
  %10 = call i32 %8(i64 noundef %1, i64 noundef %9) #5
  %11 = icmp eq i32 %10, 0
  br i1 %11, label %68, label %12

12:                                               ; preds = %5
  %13 = icmp slt i32 %10, 0
  %14 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 2
  %15 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 3
  %16 = select i1 %13, ptr %14, ptr %15
  %17 = load ptr, ptr %16, align 8, !tbaa !26
  %18 = icmp eq ptr %17, null
  br i1 %18, label %68, label %19

19:                                               ; preds = %12
  %20 = load ptr, ptr %7, align 8, !tbaa !10
  %21 = load i64, ptr %17, align 8, !tbaa !16
  %22 = call i32 %20(i64 noundef %1, i64 noundef %21) #5
  %23 = icmp eq i32 %22, 0
  br i1 %23, label %36, label %24

24:                                               ; preds = %19
  %25 = icmp slt i32 %22, 0
  br i1 %25, label %26, label %30

26:                                               ; preds = %24
  %27 = getelementptr inbounds %struct.splay_tree_node_s, ptr %17, i64 0, i32 2, !tbaa !23
  %28 = load ptr, ptr %27, align 8, !tbaa !23
  %29 = icmp eq ptr %28, null
  br i1 %29, label %36, label %30

30:                                               ; preds = %26, %24
  %31 = icmp sgt i32 %22, 0
  br i1 %31, label %32, label %39

32:                                               ; preds = %30
  %33 = getelementptr inbounds %struct.splay_tree_node_s, ptr %17, i64 0, i32 3, !tbaa !24
  %34 = load ptr, ptr %33, align 8, !tbaa !24
  %35 = icmp eq ptr %34, null
  br i1 %35, label %36, label %39

36:                                               ; preds = %32, %26, %19
  br i1 %13, label %37, label %38

37:                                               ; preds = %36
  call fastcc void @rotate_left(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef nonnull %17)
  br label %68

38:                                               ; preds = %36
  call fastcc void @rotate_right(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef nonnull %17)
  br label %68

39:                                               ; preds = %32, %30
  %40 = select i1 %13, i1 %25, i1 false
  br i1 %40, label %41, label %46

41:                                               ; preds = %39
  %42 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 2, !tbaa !23
  %43 = getelementptr inbounds %struct.splay_tree_node_s, ptr %17, i64 0, i32 2, !tbaa !23
  %44 = load ptr, ptr %43, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %42, ptr noundef nonnull %17, ptr noundef %44)
  %45 = load ptr, ptr %42, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %45)
  br label %68

46:                                               ; preds = %39
  %47 = icmp sgt i32 %10, 0
  %48 = select i1 %47, i1 %31, i1 false
  br i1 %48, label %49, label %54

49:                                               ; preds = %46
  %50 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 3, !tbaa !24
  %51 = getelementptr inbounds %struct.splay_tree_node_s, ptr %17, i64 0, i32 3, !tbaa !24
  %52 = load ptr, ptr %51, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %50, ptr noundef nonnull %17, ptr noundef %52)
  %53 = load ptr, ptr %50, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %53)
  br label %68

54:                                               ; preds = %46
  %55 = select i1 %13, i1 %31, i1 false
  br i1 %55, label %56, label %61

56:                                               ; preds = %54
  %57 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 2, !tbaa !23
  %58 = getelementptr inbounds %struct.splay_tree_node_s, ptr %17, i64 0, i32 3, !tbaa !24
  %59 = load ptr, ptr %58, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %57, ptr noundef nonnull %17, ptr noundef %59)
  %60 = load ptr, ptr %57, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %60)
  br label %68

61:                                               ; preds = %54
  %62 = select i1 %47, i1 %25, i1 false
  br i1 %62, label %63, label %68

63:                                               ; preds = %61
  %64 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 3, !tbaa !24
  %65 = getelementptr inbounds %struct.splay_tree_node_s, ptr %17, i64 0, i32 2, !tbaa !23
  %66 = load ptr, ptr %65, align 8, !tbaa !23
  call fastcc void @rotate_left(ptr noundef nonnull %64, ptr noundef nonnull %17, ptr noundef %66)
  %67 = load ptr, ptr %64, align 8, !tbaa !24
  call fastcc void @rotate_right(ptr noundef nonnull %0, ptr noundef nonnull %6, ptr noundef %67)
  br label %68

68:                                               ; preds = %41, %56, %63, %61, %49, %37, %38, %12, %5
  %69 = phi i1 [ true, %5 ], [ true, %12 ], [ true, %38 ], [ true, %37 ], [ false, %49 ], [ false, %61 ], [ false, %63 ], [ false, %56 ], [ false, %41 ]
  %70 = icmp ult i1 %69, true
  br i1 %70, label %5, label %71

71:                                               ; preds = %68, %2
  ret void
}
```

```
static void
splay_tree_splay (splay_tree sp, splay_tree_key key)
{
  if (sp->root == 0)
    return;

  do {
    int cmp1, cmp2;
    splay_tree_node n, c;

    n = sp->root;
    cmp1 = (*sp->comp) (key, n->key);

    /* Found.  */
    if (cmp1 == 0)
      return;

    /* Left or right?  If no child, then we're done.  */
    if (cmp1 < 0)
      c = n->left;
    else
      c = n->right;
    if (!c)
      return;

    /* Next one left or right?  If found or no child, we're done
       after one rotation.  */
    cmp2 = (*sp->comp) (key, c->key);
    if (cmp2 == 0
        || (cmp2 < 0 && !c->left)
        || (cmp2 > 0 && !c->right))
      {
	if (cmp1 < 0)
	  rotate_left (&sp->root, n, c);
	else
	  rotate_right (&sp->root, n, c);
        return;
      }

    /* Now we have the four cases of double-rotation.  */
    if (cmp1 < 0 && cmp2 < 0)
      {
	rotate_left (&n->left, c, c->left);
	rotate_left (&sp->root, n, n->left);
      }
    else if (cmp1 > 0 && cmp2 > 0)
      {
	rotate_right (&n->right, c, c->right);
	rotate_right (&sp->root, n, n->right);
      }
    else if (cmp1 < 0 && cmp2 > 0)
      {
	rotate_right (&n->left, c, c->right);
	rotate_left (&sp->root, n, n->left);
      }
    else if (cmp1 > 0 && cmp2 < 0)
      {
	rotate_left (&n->right, c, c->left);
	rotate_right (&sp->root, n, n->right);
      }
  } while (1);
}
```

```
struct splay_tree_node_s {
  /* The key.  */
  splay_tree_key key;

  /* The value.  */
  splay_tree_value value;

  /* The left and right children, respectively.  */
  splay_tree_node left;
  splay_tree_node right;
};
```

