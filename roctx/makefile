#-------------------------------------------------------------
# optionally add an underscore to all Fortran entry points
#-------------------------------------------------------------
 FNAME = -DSKIP_FORTRAN
#FNAME = -DADD_UNDERSCORE

#-------------------------------------------------------------
# normally use gcc to build the wrapper libraries
# add -DHAS_INIT_THREAD if your MPI implementation supports MPI_Init_thread()
#-------------------------------------------------------------
CC = mpicc

BUILD_ROCM_LEGACY = 1
BUILD_ROCM = 1

ROCM_INC_LEGACY = -I /capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/include -I /capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/include/roctracer
ROCM_LIB_LEGACY = -L /capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/lib -lroctracer64 -lroctx64 -Wl,-rpath=/capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/lib
CFLAGS_LEGACY = -g -O -fPIC $(VPROF) -I. $(MPI_INC) $(ROCM_INC_LEGACY) -DUSE_ROCTX -DUSE_ROCTX_LEGACY -DHAS_INIT_THREAD

ROCM_INC = -I /capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/include -I /capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/include/rocprofiler-sdk-roctx
ROCM_LIB = -L /capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/lib -lrocprofiler-sdk -Wl,-rpath=/capstor/scratch/cscs/sfantao/build-megatron/sw/rocm-6.2.4/lib
CFLAGS = -g -O -fPIC $(VPROF) -I. $(MPI_INC) $(ROCM_INC) -DUSE_ROCTX  -DHAS_INIT_THREAD

#------------------------------------------
# targets
#------------------------------------------

ifeq ($(BUILD_ROCM_LEGACY),1)
	shared_targets += libmpitrace-legacy.so 
	static_targets += libmpitrace-legacy.a 
endif
ifeq ($(BUILD_ROCM),1)
	shared_targets += libmpitrace.so 
	static_targets += libmpitrace.a 
endif

default: shared #static 

static : $(static_targets)

shared : $(shared_targets)

clean :
	rm -f *.a *.o *.so *.lst 

distclean :
	rm -f *.a *.o *.so *.lst makefile

#------------------------------------------------------------------
# rules for the wrapper libraries - add support for vprof profiling
#------------------------------------------------------------------
libmpitrace.a : mpitrace.o mpitrace_sortx.o      $(VPROF_STATIC_OBJ)
	ar crv $@ mpitrace.o mpitrace_sortx.o    $(VPROF_STATIC_OBJ)

libmpitrace-legacy.a : mpitrace-legacy.o mpitrace_sortx.o      $(VPROF_STATIC_OBJ)
	ar crv $@ mpitrace-legacy.o mpitrace_sortx.o    $(VPROF_STATIC_OBJ)

libmpitrace.so : mpitrace.o mpitrace_sortx.o                             $(VPROF_DYNAMIC_OBJ)
	$(CC) -o $@ -shared mpitrace.o   mpitrace_sortx.o  $(VPROF_DYNAMIC_OBJ) $(FLIB) $(BFD_LIB) $(ROCM_LIB)

libmpitrace-legacy.so : mpitrace-legacy.o mpitrace_sortx.o                             $(VPROF_DYNAMIC_OBJ)
	$(CC) -o $@ -shared mpitrace-legacy.o   mpitrace_sortx.o  $(VPROF_DYNAMIC_OBJ) $(FLIB) $(BFD_LIB) $(ROCM_LIB_LEGACY)
#------------------------------------------
# rules for .o files
#------------------------------------------
mpitrace.o : mpitrace.c mpitrace_common.c  init_part1.c init_part2.c \
              fortran_wrappers.c mpitrace.h
	$(CC) -c $(CFLAGS) mpitrace.c -o $@

mpitrace-legacy.o : mpitrace.c mpitrace_common.c  init_part1.c init_part2.c \
              fortran_wrappers.c mpitrace.h
	$(CC) -c $(CFLAGS_LEGACY) mpitrace.c -o $@

mpitrace_sortx.o : mpitrace_sortx.c
	$(CC) -c $(CFLAGS) mpitrace_sortx.c -o $@

