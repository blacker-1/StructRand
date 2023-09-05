## TBAA：BaseType问题

原生LLVM中，根据TBAA生成原理，我们的GEP指令生成的TBAA类似于load和store指令，BaseType是基于最外层结构体的。