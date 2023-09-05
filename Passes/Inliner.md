## Inliner Pass

内联函数，可以保留TBAA。

函数：

```
int re_search_2 (struct re_pattern_buffer *bufp, const char *string1, int size1,const char *string2, int size2, int startpos, int range, struct re_registers *regs, int stop)
{
	# ifdef MBS_SUPPORT
		if (MB_CUR_MAX != 1)
			return wcs_re_search_2 (bufp, string1, size1, string2, size2, startpos, range, regs, stop);
	else
	# endif
		return byte_re_search_2 (bufp, string1, size1, string2, size2, startpos, range, regs, stop);
} /* re_search_2 */
```

IR为：

```
; Function Attrs: nounwind uwtable
define dso_local i32 @xre_search_2(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, i32 noundef %4, i32 noundef %5, i32 noundef %6, ptr noundef %7, i32 noundef %8) local_unnamed_addr #2 [0] {
  %10 = call fastcc i32 @byte_re_search_2(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, i32 noundef %4, i32 noundef %5, i32 noundef %6, ptr noundef %7, i32 noundef %8)
  ret i32 %10
}
```

调用了：

```
; Function Attrs: nounwind uwtable
define internal fastcc i32 @byte_re_search_2(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, i32 noundef %4, i32 noundef %5, i32 noundef %6, ptr noundef %7, i32 noundef %8) unnamed_addr #2 [18] {
  %10 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 4, !tbaa !5
  %11 = load ptr, ptr %10, align 8, !tbaa !5
  %12 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 5, !tbaa !12
  %13 = load ptr, ptr %12, align 8, !tbaa !12
  %14 = add nsw i32 %2, %4
  %15 = add nsw i32 %5, %6
  %16 = icmp slt i32 %5, 0
  %17 = icmp slt i32 %14, %5
  %18 = select i1 %16, i1 true, i1 %17
  br i1 %18, label %164, label %19

19:                                               ; preds = %9
  %20 = icmp slt i32 %15, 0
  %21 = sub nsw i32 0, %5
  %22 = icmp sgt i32 %15, %14
  %23 = sub nsw i32 %14, %5
  %24 = select i1 %22, i32 %23, i32 %6
  %25 = select i1 %20, i32 %21, i32 %24
  %26 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 2, !tbaa !13
  %27 = load i64, ptr %26, align 8, !tbaa !13
  %28 = icmp ne i64 %27, 0
  %29 = icmp sgt i32 %25, 0
  %30 = select i1 %28, i1 %29, i1 false
  br i1 %30, label %31, label %40

31:                                               ; preds = %19
  %32 = load ptr, ptr %0, align 8, !tbaa !14
  %33 = load i8, ptr %32, align 1, !tbaa !15
  switch i8 %33, label %40 [
    i8 11, label %38
    i8 9, label %34
  ]

34:                                               ; preds = %31
  %35 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %36 = load i8, ptr %35, align 8
  %37 = icmp sgt i8 %36, -1
  br i1 %37, label %38, label %40

38:                                               ; preds = %31, %34
  %39 = icmp sgt i32 %5, 0
  br i1 %39, label %164, label %40

40:                                               ; preds = %38, %31, %34, %19
  %41 = phi i32 [ %25, %34 ], [ %25, %19 ], [ %25, %31 ], [ 1, %38 ]
  %42 = icmp ne ptr %11, null
  br i1 %42, label %43, label %51

43:                                               ; preds = %40
  %44 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %45 = load i8, ptr %44, align 8
  %46 = and i8 %45, 8
  %47 = icmp eq i8 %46, 0
  br i1 %47, label %48, label %51

48:                                               ; preds = %43
  %49 = call fastcc i32 @byte_re_compile_fastmap(ptr noundef %0)
  %50 = icmp eq i32 %49, -2
  br i1 %50, label %164, label %51

51:                                               ; preds = %48, %43, %40
  br label %52

52:                                               ; preds = %156, %51
  %53 = phi i32 [ %41, %51 ], [ %162, %156 ]
  %54 = phi i32 [ %5, %51 ], [ %163, %156 ]
  %55 = icmp slt i32 %54, %14
  %56 = select i1 %42, i1 %55, i1 false
  br i1 %56, label %57, label %135

57:                                               ; preds = %52
  %58 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %59 = load i8, ptr %58, align 8
  %60 = and i8 %59, 1
  %61 = icmp eq i8 %60, 0
  br i1 %61, label %62, label %135

62:                                               ; preds = %57
  %63 = icmp sgt i32 %53, 0
  br i1 %63, label %64, label %113

64:                                               ; preds = %62
  %65 = icmp sge i32 %54, %2
  %66 = add nsw i32 %54, %53
  %67 = icmp slt i32 %66, %2
  %68 = select i1 %65, i1 true, i1 %67
  %69 = sub i32 %54, %2
  %70 = add i32 %69, %53
  %71 = select i1 %68, i32 0, i32 %70
  %72 = icmp slt i32 %54, %2
  %73 = sext i32 %2 to i64
  %74 = sub nsw i64 0, %73
  %75 = getelementptr inbounds i8, ptr %3, i64 %74
  %76 = select i1 %72, ptr %1, ptr %75
  %77 = sext i32 %54 to i64
  %78 = getelementptr inbounds i8, ptr %76, i64 %77
  %79 = icmp eq ptr %13, null
  br i1 %79, label %96, label %80

80:                                               ; preds = %64, %93
  %81 = phi i32 [ %53, %64 ], [ %95, %93 ]
  %82 = phi ptr [ %78, %64 ], [ %94, %93 ]
  %83 = icmp sgt i32 %81, %71
  br i1 %83, label %84, label %109

84:                                               ; preds = %80
  %85 = load i8, ptr %82, align 1, !tbaa !15
  %86 = zext i8 %85 to i64
  %87 = getelementptr inbounds i8, ptr %13, i64 %86
  %88 = load i8, ptr %87, align 1, !tbaa !15
  %89 = zext i8 %88 to i64
  %90 = getelementptr inbounds i8, ptr %11, i64 %89
  %91 = load i8, ptr %90, align 1, !tbaa !15
  %92 = icmp eq i8 %91, 0
  br i1 %92, label %93, label %109

93:                                               ; preds = %84
  %94 = getelementptr inbounds i8, ptr %82, i64 1
  %95 = add nsw i32 %81, -1
  br label %80, !llvm.loop !16

96:                                               ; preds = %64, %106
  %97 = phi i32 [ %53, %64 ], [ %108, %106 ]
  %98 = phi ptr [ %78, %64 ], [ %107, %106 ]
  %99 = icmp sgt i32 %97, %71
  br i1 %99, label %100, label %109

100:                                              ; preds = %96
  %101 = load i8, ptr %98, align 1, !tbaa !15
  %102 = zext i8 %101 to i64
  %103 = getelementptr inbounds i8, ptr %11, i64 %102
  %104 = load i8, ptr %103, align 1, !tbaa !15
  %105 = icmp eq i8 %104, 0
  br i1 %105, label %106, label %109

106:                                              ; preds = %100
  %107 = getelementptr inbounds i8, ptr %98, i64 1
  %108 = add nsw i32 %97, -1
  br label %96, !llvm.loop !19

109:                                              ; preds = %100, %96, %84, %80
  %110 = phi i32 [ %81, %84 ], [ %81, %80 ], [ %97, %100 ], [ %97, %96 ]
  %111 = sub nsw i32 %53, %110
  %112 = add nsw i32 %54, %111
  br label %135

113:                                              ; preds = %62
  %114 = icmp ne i32 %2, 0
  %115 = icmp slt i32 %54, %2
  %116 = select i1 %114, i1 %115, i1 false
  %117 = sub nsw i32 %54, %2
  %118 = sext i32 %117 to i64
  %119 = getelementptr inbounds i8, ptr %3, i64 %118
  %120 = sext i32 %54 to i64
  %121 = getelementptr inbounds i8, ptr %1, i64 %120
  %122 = select i1 %116, ptr %121, ptr %119
  %123 = load i8, ptr %122, align 1, !tbaa !15
  %124 = icmp eq ptr %13, null
  br i1 %124, label %129, label %125

125:                                              ; preds = %113
  %126 = zext i8 %123 to i64
  %127 = getelementptr inbounds i8, ptr %13, i64 %126
  %128 = load i8, ptr %127, align 1, !tbaa !15
  br label %129

129:                                              ; preds = %113, %125
  %130 = phi i8 [ %128, %125 ], [ %123, %113 ]
  %131 = zext i8 %130 to i64
  %132 = getelementptr inbounds i8, ptr %11, i64 %131
  %133 = load i8, ptr %132, align 1, !tbaa !15
  %134 = icmp eq i8 %133, 0
  br i1 %134, label %152, label %135

135:                                              ; preds = %129, %109, %57, %52
  %136 = phi i32 [ %53, %57 ], [ %110, %109 ], [ %53, %129 ], [ %53, %52 ]
  %137 = phi i32 [ %54, %57 ], [ %112, %109 ], [ %54, %129 ], [ %54, %52 ]
  %138 = icmp sgt i32 %136, -1
  %139 = icmp eq i32 %137, %14
  %140 = select i1 %139, i1 %42, i1 false
  %141 = select i1 %138, i1 %140, i1 false
  br i1 %141, label %142, label %147

142:                                              ; preds = %135
  %143 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %144 = load i8, ptr %143, align 8
  %145 = and i8 %144, 1
  %146 = icmp eq i8 %145, 0
  br i1 %146, label %164, label %147

147:                                              ; preds = %142, %135
  %148 = call fastcc i32 @byte_re_match_2_internal(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, i32 noundef %4, i32 noundef %137, ptr noundef %7, i32 noundef %8)
  %149 = icmp sgt i32 %148, -1
  br i1 %149, label %164, label %150

150:                                              ; preds = %147
  %151 = icmp eq i32 %148, -2
  br i1 %151, label %164, label %152

152:                                              ; preds = %129, %150
  %153 = phi i32 [ %136, %150 ], [ %53, %129 ]
  %154 = phi i32 [ %137, %150 ], [ %54, %129 ]
  %155 = icmp eq i32 %153, 0
  br i1 %155, label %164, label %156

156:                                              ; preds = %152
  %157 = icmp sgt i32 %153, 0
  %158 = add nsw i32 %153, -1
  %159 = add nsw i32 %154, 1
  %160 = add nsw i32 %153, 1
  %161 = add nsw i32 %154, -1
  %162 = select i1 %157, i32 %158, i32 %160
  %163 = select i1 %157, i32 %159, i32 %161
  br label %52, !llvm.loop !20

164:                                              ; preds = %152, %150, %147, %142, %48, %38, %9
  %165 = phi i32 [ -1, %9 ], [ -1, %38 ], [ -2, %48 ], [ -1, %142 ], [ %137, %147 ], [ -2, %150 ], [ -1, %152 ]
  ret i32 %165
}
```

内联后：

```
*** IR Dump After InlinerPass on (xre_search_2) ***
; Function Attrs: nounwind uwtable
define dso_local i32 @xre_search_2(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, i32 noundef %4, i32 noundef %5, i32 noundef %6, ptr noundef %7, i32 noundef %8) local_unnamed_addr #2 [18] {
  %10 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 4, !tbaa !5
  %11 = load ptr, ptr %10, align 8, !tbaa !5
  %12 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 5, !tbaa !12
  %13 = load ptr, ptr %12, align 8, !tbaa !12
  %14 = add nsw i32 %4, %2
  %15 = icmp slt i32 %5, 0
  %16 = icmp slt i32 %14, %5
  %17 = select i1 %15, i1 true, i1 %16
  br i1 %17, label %164, label %18

18:                                               ; preds = %9
  %19 = add nsw i32 %6, %5
  %20 = icmp slt i32 %19, 0
  %21 = sub nsw i32 0, %5
  %22 = icmp sgt i32 %19, %14
  %23 = sub nsw i32 %14, %5
  %24 = select i1 %22, i32 %23, i32 %6
  %25 = select i1 %20, i32 %21, i32 %24
  %26 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 2, !tbaa !13
  %27 = load i64, ptr %26, align 8, !tbaa !13
  %28 = icmp ne i64 %27, 0
  %29 = icmp sgt i32 %25, 0
  %30 = select i1 %28, i1 %29, i1 false
  br i1 %30, label %31, label %40

31:                                               ; preds = %18
  %32 = load ptr, ptr %0, align 8, !tbaa !14
  %33 = load i8, ptr %32, align 1, !tbaa !15
  switch i8 %33, label %40 [
    i8 11, label %38
    i8 9, label %34
  ]

34:                                               ; preds = %31
  %35 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %36 = load i8, ptr %35, align 8
  %37 = icmp sgt i8 %36, -1
  br i1 %37, label %38, label %40

38:                                               ; preds = %34, %31
  %39 = icmp sgt i32 %5, 0
  br i1 %39, label %164, label %40

40:                                               ; preds = %38, %34, %31, %18
  %41 = phi i32 [ %25, %34 ], [ %25, %18 ], [ %25, %31 ], [ 1, %38 ]
  %42 = icmp ne ptr %11, null
  br i1 %42, label %43, label %51

43:                                               ; preds = %40
  %44 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %45 = load i8, ptr %44, align 8
  %46 = and i8 %45, 8
  %47 = icmp eq i8 %46, 0
  br i1 %47, label %48, label %51

48:                                               ; preds = %43
  %49 = call fastcc i32 @byte_re_compile_fastmap(ptr noundef %0)
  %50 = icmp eq i32 %49, -2
  br i1 %50, label %164, label %51

51:                                               ; preds = %48, %43, %40
  %52 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %53 = getelementptr inbounds %struct.re_pattern_buffer, ptr %0, i64 0, i32 7
  %54 = icmp ne i32 %2, 0
  %55 = icmp eq ptr %13, null
  %56 = sext i32 %2 to i64
  %57 = sub nsw i64 0, %56
  %58 = getelementptr inbounds i8, ptr %3, i64 %57
  %59 = icmp eq ptr %13, null
  br label %60

60:                                               ; preds = %158, %51
  %61 = phi i32 [ %41, %51 ], [ %161, %158 ]
  %62 = phi i32 [ %5, %51 ], [ %163, %158 ]
  %63 = icmp slt i32 %62, %14
  %64 = select i1 %42, i1 %63, i1 false
  br i1 %64, label %65, label %138

65:                                               ; preds = %60
  %66 = load i8, ptr %53, align 8
  %67 = and i8 %66, 1
  %68 = icmp eq i8 %67, 0
  br i1 %68, label %69, label %138

69:                                               ; preds = %65
  %70 = icmp sgt i32 %61, 0
  br i1 %70, label %71, label %118

71:                                               ; preds = %69
  %72 = icmp sge i32 %62, %2
  %73 = add nsw i32 %62, %61
  %74 = icmp slt i32 %73, %2
  %75 = select i1 %72, i1 true, i1 %74
  %76 = sub i32 %61, %2
  %77 = add i32 %76, %62
  %78 = select i1 %75, i32 0, i32 %77
  %79 = icmp slt i32 %62, %2
  %80 = select i1 %79, ptr %1, ptr %58
  %81 = sext i32 %62 to i64
  %82 = getelementptr inbounds i8, ptr %80, i64 %81
  br i1 %59, label %85, label %83

83:                                               ; preds = %71
  %84 = icmp sgt i32 %61, %78
  br i1 %84, label %87, label %114

85:                                               ; preds = %71
  %86 = icmp sgt i32 %61, %78
  br i1 %86, label %102, label %114

87:                                               ; preds = %98, %83
  %88 = phi ptr [ %99, %98 ], [ %82, %83 ]
  %89 = phi i32 [ %100, %98 ], [ %61, %83 ]
  %90 = load i8, ptr %88, align 1, !tbaa !15
  %91 = zext i8 %90 to i64
  %92 = getelementptr inbounds i8, ptr %13, i64 %91
  %93 = load i8, ptr %92, align 1, !tbaa !15
  %94 = zext i8 %93 to i64
  %95 = getelementptr inbounds i8, ptr %11, i64 %94
  %96 = load i8, ptr %95, align 1, !tbaa !15
  %97 = icmp eq i8 %96, 0
  br i1 %97, label %98, label %114

98:                                               ; preds = %87
  %99 = getelementptr inbounds i8, ptr %88, i64 1
  %100 = add nsw i32 %89, -1
  %101 = icmp sgt i32 %100, %78
  br i1 %101, label %87, label %114, !llvm.loop !16

102:                                              ; preds = %110, %85
  %103 = phi ptr [ %111, %110 ], [ %82, %85 ]
  %104 = phi i32 [ %112, %110 ], [ %61, %85 ]
  %105 = load i8, ptr %103, align 1, !tbaa !15
  %106 = zext i8 %105 to i64
  %107 = getelementptr inbounds i8, ptr %11, i64 %106
  %108 = load i8, ptr %107, align 1, !tbaa !15
  %109 = icmp eq i8 %108, 0
  br i1 %109, label %110, label %114

110:                                              ; preds = %102
  %111 = getelementptr inbounds i8, ptr %103, i64 1
  %112 = add nsw i32 %104, -1
  %113 = icmp sgt i32 %112, %78
  br i1 %113, label %102, label %114, !llvm.loop !19

114:                                              ; preds = %110, %102, %98, %87, %85, %83
  %115 = phi i32 [ %61, %85 ], [ %61, %83 ], [ %104, %102 ], [ %78, %110 ], [ %89, %87 ], [ %78, %98 ]
  %116 = add i32 %62, %61
  %117 = sub i32 %116, %115
  br label %138

118:                                              ; preds = %69
  %119 = icmp slt i32 %62, %2
  %120 = select i1 %54, i1 %119, i1 false
  %121 = sub nsw i32 %62, %2
  %122 = sext i32 %121 to i64
  %123 = getelementptr inbounds i8, ptr %3, i64 %122
  %124 = sext i32 %62 to i64
  %125 = getelementptr inbounds i8, ptr %1, i64 %124
  %126 = select i1 %120, ptr %125, ptr %123
  %127 = load i8, ptr %126, align 1, !tbaa !15
  br i1 %55, label %132, label %128

128:                                              ; preds = %118
  %129 = zext i8 %127 to i64
  %130 = getelementptr inbounds i8, ptr %13, i64 %129
  %131 = load i8, ptr %130, align 1, !tbaa !15
  br label %132

132:                                              ; preds = %128, %118
  %133 = phi i8 [ %131, %128 ], [ %127, %118 ]
  %134 = zext i8 %133 to i64
  %135 = getelementptr inbounds i8, ptr %11, i64 %134
  %136 = load i8, ptr %135, align 1, !tbaa !15
  %137 = icmp eq i8 %136, 0
  br i1 %137, label %154, label %138

138:                                              ; preds = %132, %114, %65, %60
  %139 = phi i32 [ %61, %65 ], [ %115, %114 ], [ %61, %132 ], [ %61, %60 ]
  %140 = phi i32 [ %62, %65 ], [ %117, %114 ], [ %62, %132 ], [ %62, %60 ]
  %141 = icmp sgt i32 %139, -1
  %142 = icmp eq i32 %140, %14
  %143 = select i1 %141, i1 %142, i1 false
  %144 = select i1 %143, i1 %42, i1 false
  br i1 %144, label %145, label %149

145:                                              ; preds = %138
  %146 = load i8, ptr %52, align 8
  %147 = and i8 %146, 1
  %148 = icmp eq i8 %147, 0
  br i1 %148, label %164, label %149

149:                                              ; preds = %145, %138
  %150 = call fastcc i32 @byte_re_match_2_internal(ptr noundef %0, ptr noundef %1, i32 noundef %2, ptr noundef %3, i32 noundef %4, i32 noundef %140, ptr noundef %7, i32 noundef %8)
  %151 = icmp sgt i32 %150, -1
  br i1 %151, label %164, label %152

152:                                              ; preds = %149
  %153 = icmp eq i32 %150, -2
  br i1 %153, label %164, label %154

154:                                              ; preds = %152, %132
  %155 = phi i32 [ %139, %152 ], [ %61, %132 ]
  %156 = phi i32 [ %140, %152 ], [ %62, %132 ]
  %157 = icmp eq i32 %155, 0
  br i1 %157, label %164, label %158

158:                                              ; preds = %154
  %159 = icmp sgt i32 %155, 0
  %160 = select i1 %159, i32 -1, i32 1
  %161 = add nsw i32 %160, %155
  %162 = select i1 %159, i32 1, i32 -1
  %163 = add nsw i32 %162, %156
  br label %60, !llvm.loop !20

164:                                              ; preds = %9, %38, %48, %145, %149, %152, %154
  %165 = phi i32 [ -1, %9 ], [ -1, %38 ], [ -2, %48 ], [ -1, %145 ], [ %140, %149 ], [ -2, %152 ], [ -1, %154 ]
  ret i32 %165
}
```

