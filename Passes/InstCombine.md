## InstCombine

合并两个GEP:

1. 两个GEP均带有TBAA：合并两个TBAA

2. 只有一个带有TBAA：禁止合并

3. 都没有TBAA：不处理（不修改LLVM）