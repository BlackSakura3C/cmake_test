add_library(MathFunctions mysqrt.cxx)

install(TARGETS MathFunctions DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)