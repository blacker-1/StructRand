## InstCombine

合并两个GEP:

1. 两个GEP均带有TBAA：合并两个TBAA

2. 只有一个带有TBAA：禁止合并

3. 都没有TBAA：不处理（不修改LLVM）

### 1.两个GEP均带有TBAA：合并两个TBAA

修改前：

```c
#include <stdio.h>

struct test2
{
	int a;
	int b;
	char name[20];
	struct Data
	{
		int i;
		float f;
		char str[20];
	} data;
	int c;
};

struct test
{
	int a;
	int b;
	struct test2 t;
};

void test1(struct test *t1)
{
	printf("%d", t1->t.a);
	printf("%d", t1->t.b);
	printf("%s", t1->t.data.str);
	printf("%d", t1->t.data.i);
}
int main()
{
	struct test t1;
	t1.t.b = 10;
	printf("%d\n", t1.t.b);
	test1(&t1);
}
```



```c
### IR Dump Before InstCombinePass on main
define dso_local i32 @main() local_unnamed_addr #0 !dbg !75 {
  %1 = alloca %struct.test, align 4
  call void @llvm.lifetime.start.p0(i64 68, ptr %1) #5, !dbg !80
  call void @llvm.dbg.declare(metadata ptr %1, metadata !79, metadata !DIExpression()), !dbg !81
  (合并前指令)%2 = getelementptr inbounds %struct.test, ptr %1, i32 0, i32 2, !dbg !82, !tbaa !53
  (合并前指令)%3 = getelementptr inbounds %struct.test2, ptr %2, i32 0, i32 1, !dbg !83, !tbaa !65
  store i32 10, ptr %3, align 4, !dbg !84, !tbaa !65
  %4 = call i32 (ptr, ...) @printf(ptr noundef @.str.2, i32 noundef 10), !dbg !85
  call void @test1(ptr noundef %1), !dbg !86
  call void @llvm.lifetime.end.p0(i64 68, ptr %1) #5, !dbg !87
  ret i32 0, !dbg !87
}
### IR Dump After InstCombinePass on main
define dso_local i32 @main() local_unnamed_addr #0 !dbg !74 {
  %1 = alloca %struct.test, align 4
  call void @llvm.lifetime.start.p0(i64 68, ptr nonnull %1) #5, !dbg !79
  call void @llvm.dbg.declare(metadata ptr %1, metadata !78, metadata !DIExpression()), !dbg !80
  (合并后指令)%2 = getelementptr inbounds %struct.test, ptr %1, i64 0, i32 2, i32 1, !dbg !81 (丢失TBAA)
  store i32 10, ptr %2, align 4, !dbg !82, !tbaa !65
  %3 = call i32 (ptr, ...) @printf(ptr noundef nonnull @.str.2, i32 noundef 10), !dbg !83
  call void @test1(ptr noundef nonnull %1), !dbg !84
  call void @llvm.lifetime.end.p0(i64 68, ptr nonnull %1) #5, !dbg !85
  ret i32 0, !dbg !85
}
```

### 2.只有一个带有TBAA：禁止合并

```
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
   {{0.0, 11}, 30},
   {{0.0, 12}, 40}
  };
  struct number *num1 = &num[1];
  load_test(&(num1->integer));

  return 0;
}
```



```
### IR Dump Before InstCombinePass on main
define dso_local i32 @main() local_unnamed_addr #3 !dbg !38 {
  %1 = alloca [3 x %struct.number], align 16
  call void @llvm.lifetime.start.p0(i64 36, ptr %1) #7, !dbg !57
  call void @llvm.dbg.declare(metadata ptr %1, metadata !42, metadata !DIExpression()), !dbg !58
  call void @llvm.memset.p0.i64(ptr align 16 %1, i8 0, i64 36, i1 false), !dbg !58
  %2 = getelementptr inbounds %struct.complex, ptr %1, i32 0, i32 1, !dbg !58
  store i32 10, ptr %2, align 4, !dbg !58
  %3 = getelementptr inbounds %struct.number, ptr %1, i32 0, i32 1, !dbg !58
  store i32 20, ptr %3, align 8, !dbg !58
  %4 = getelementptr inbounds [3 x %struct.number], ptr %1, i32 0, i32 1, !dbg !58
  %5 = getelementptr inbounds %struct.complex, ptr %4, i32 0, i32 1, !dbg !58
  store i32 11, ptr %5, align 4, !dbg !58
  %6 = getelementptr inbounds %struct.number, ptr %4, i32 0, i32 1, !dbg !58
  store i32 30, ptr %6, align 4, !dbg !58
  %7 = getelementptr inbounds [3 x %struct.number], ptr %1, i32 0, i32 2, !dbg !58
  %8 = getelementptr inbounds %struct.complex, ptr %7, i32 0, i32 1, !dbg !58
  store i32 12, ptr %8, align 4, !dbg !58
  %9 = getelementptr inbounds %struct.number, ptr %7, i32 0, i32 1, !dbg !58
  store i32 40, ptr %9, align 8, !dbg !58
  %10 = getelementptr inbounds [3 x %struct.number], ptr %1, i64 0, i64 1, !dbg !59
  call void @llvm.dbg.value(metadata ptr %10, metadata !55, metadata !DIExpression()), !dbg !60
  %11 = getelementptr inbounds %struct.number, ptr %10, i32 0, i32 1, !dbg !61, !tbaa !62
  call void @load_test(ptr noundef %11), !dbg !66
  call void @llvm.lifetime.end.p0(i64 36, ptr %1) #7, !dbg !67
  ret i32 0, !dbg !68
}
### IR Dump After InstCombinePass on main
define dso_local i32 @main() local_unnamed_addr #3 !dbg !38 {
  %1 = alloca [3 x %struct.number], align 16
  call void @llvm.lifetime.start.p0(i64 36, ptr nonnull %1) #7, !dbg !57
  call void @llvm.dbg.declare(metadata ptr %1, metadata !42, metadata !DIExpression()), !dbg !58
  call void @llvm.memset.p0.i64(ptr noundef nonnull align 16 dereferenceable(36) %1, i8 0, i64 36, i1 false), !dbg !58
  %2 = getelementptr inbounds %struct.complex, ptr %1, i64 0, i32 1, !dbg !58
  store i32 10, ptr %2, align 4, !dbg !58
  %3 = getelementptr inbounds %struct.number, ptr %1, i64 0, i32 1, !dbg !58
  store i32 20, ptr %3, align 8, !dbg !58
  %4 = getelementptr inbounds [3 x %struct.number], ptr %1, i64 0, i64 1, i32 0, i32 1, !dbg !58
  store i32 11, ptr %4, align 16, !dbg !58
  %5 = getelementptr inbounds [3 x %struct.number], ptr %1, i64 0, i64 1, i32 1, !dbg !58
  store i32 30, ptr %5, align 4, !dbg !58
  %6 = getelementptr inbounds [3 x %struct.number], ptr %1, i64 0, i64 2, i32 0, i32 1, !dbg !58
  store i32 12, ptr %6, align 4, !dbg !58
  %7 = getelementptr inbounds [3 x %struct.number], ptr %1, i64 0, i64 2, i32 1, !dbg !58
  store i32 40, ptr %7, align 16, !dbg !58
  call void @llvm.dbg.value(metadata ptr %1, metadata !55, metadata !DIExpression(DW_OP_plus_uconst, 12, DW_OP_stack_value)), !dbg !59
  %8 = getelementptr inbounds [3 x %struct.number], ptr %1, i64 0, i64 1, i32 1, !dbg !60
  call void @load_test(ptr noundef nonnull %8), !dbg !61
  call void @llvm.lifetime.end.p0(i64 36, ptr nonnull %1) #7, !dbg !62
  ret i32 0, !dbg !63
}
```

