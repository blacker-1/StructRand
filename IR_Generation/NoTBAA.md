## NoTBAA

```
# IR Dump After PostOrderFunctionAttrsPass on (splay_tree_splay)
; Function Attrs: nounwind uwtable
define internal fastcc void @splay_tree_splay(ptr nocapture noundef %0, i64 noundef %1) unnamed_addr #0 [33] {
  %3 = load ptr, ptr %0, align 8, !tbaa !5
  %4 = icmp eq ptr %3, null
  br i1 %4, label %101, label %5

5:                                                ; preds = %98, %2
  %6 = load ptr, ptr %0, align 8, !tbaa !5
  %7 = getelementptr inbounds %struct.splay_tree_s, ptr %0, i64 0, i32 1, !tbaa !10
  %8 = load ptr, ptr %7, align 8, !tbaa !10
  %9 = load i64, ptr %6, align 8, !tbaa !11
  %10 = call i32 %8(i64 noundef %1, i64 noundef %9) #5
  %11 = icmp eq i32 %10, 0
  br i1 %11, label %98, label %12

12:                                               ; preds = %5
  %13 = icmp slt i32 %10, 0
  %14 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 2
  %15 = getelementptr inbounds %struct.splay_tree_node_s, ptr %6, i64 0, i32 3
  %16 = select i1 %13, ptr %14, ptr %15
  %17 = load ptr, ptr %16, align 8, !tbaa !14
  %18 = icmp eq ptr %17, null
  br i1 %18, label %98, label %19
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

