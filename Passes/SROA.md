## SROA

GEP指令替换导致TBAA丢失

Before

```
470:                                              ; preds = %458, %499
  %471 = load ptr, ptr %12, align 8, !tbaa !24
  %472 = getelementptr inbounds %struct.inflate_state, ptr %471, i32 0, i32 20, !tbaa !50
  %473 = load ptr, ptr %472, align 8, !tbaa !50
  %474 = load i64, ptr %17, align 8, !tbaa !30
  %475 = trunc i64 %474 to i32
  %476 = load ptr, ptr %12, align 8, !tbaa !24
  %477 = getelementptr inbounds %struct.inflate_state, ptr %476, i32 0, i32 22, !tbaa !51
  %478 = load i32, ptr %477, align 8, !tbaa !51
  %479 = shl i32 1, %478
  %480 = sub i32 %479, 1
  %481 = and i32 %475, %480
  %482 = zext i32 %481 to i64
  %483 = getelementptr inbounds %struct.code, ptr %473, i64 %482
  call void @llvm.memcpy.p0.p0.i64(ptr align 2 %21, ptr align 2 %483, i64 4, i1 false), !tbaa.struct !53
  %484 = getelementptr inbounds %struct.code, ptr %21, i32 0, i32 1, !tbaa !54
  %485 = load i8, ptr %484, align 1, !tbaa !54
  %486 = zext i8 %485 to i32
  %487 = load i32, ptr %18, align 4, !tbaa !29
  %488 = icmp ule i32 %486, %487
  br i1 %488, label %513, label %489
```

After:

```
333:                                              ; preds = %321, %360
  %334 = phi i32 [ %322, %321 ], [ %362, %360 ]
  %335 = phi i64 [ %323, %321 ], [ %369, %360 ]
  %336 = phi i32 [ %324, %321 ], [ %370, %360 ]
  %337 = getelementptr inbounds %struct.inflate_state, ptr %15, i32 0, i32 20, !tbaa !48
  %338 = load ptr, ptr %337, align 8, !tbaa !48
  %339 = trunc i64 %335 to i32
  %340 = getelementptr inbounds %struct.inflate_state, ptr %15, i32 0, i32 22, !tbaa !49
  %341 = load i32, ptr %340, align 8, !tbaa !49
  %342 = shl i32 1, %341
  %343 = sub i32 %342, 1
  %344 = and i32 %339, %343
  %345 = zext i32 %344 to i64
  %346 = getelementptr inbounds %struct.code, ptr %338, i64 %345
  %347 = load i8, ptr %346, align 2, !tbaa.struct !51
  %348 = getelementptr inbounds i8, ptr %346, i64 1
  %349 = load i8, ptr %348, align 1, !tbaa.struct !52
  %350 = getelementptr inbounds i8, ptr %346, i64 2
  %351 = load i16, ptr %350, align 2, !tbaa.struct !53
  %352 = zext i8 %349 to i32
  %353 = icmp ule i32 %352, %336
  br i1 %353, label %371, label %354
```

