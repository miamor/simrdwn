GPU=1
CUDNN=1
OPENCV=0
DEBUG=1

# nvcc --version

# https://arnon.dk/matching-sm-architectures-arch-and-gencode-for-various-nvidia-cards/
ARCH=  -gencode arch=compute_50,code=[sm_50,compute_50] \
       -gencode arch=compute_52,code=[sm_52,compute_52] \
       -gencode arch=compute_60,code=[sm_60,compute_60] \
       -gencode arch=compute_61,code=[sm_61,compute_61] \
       -gencode arch=compute_62,code=[sm_62,compute_62] \
       -gencode arch=compute_70,code=[sm_70,compute_70] 
#	   -gencode arch=compute_75,code=[sm_75,compute_75]

# ARCH=  -gencode arch=compute_30,code=sm_30 \
#        -gencode arch=compute_35,code=sm_35 \
#        -gencode arch=compute_50,code=[sm_50,compute_50] \
#        -gencode arch=compute_52,code=[sm_52,compute_52] \
#        -gencode arch=compute_61,code=[sm_61,compute_61] \
#        -gencode arch=compute_62,code=[sm_62,compute_62] \
#        -gencode arch=compute_70,code=[sm_70,compute_70]

VPATH=./src/
EXEC=darknet
OBJDIR=./obj/

CC=gcc
NVCC=nvcc 
OPTS=-Ofast
LDFLAGS= -lm -pthread 
COMMON=-I/Users/avanetten/anaconda/pkgs/libgdal-2.1.0-0/include
CFLAGS=-Wall -Wfatal-errors 

ifeq ($(DEBUG), 1) 
OPTS=-O0 -g
endif

CFLAGS+=$(OPTS)

ifeq ($(OPENCV), 1) 
COMMON+= -DOPENCV
CFLAGS+= -DOPENCV
LDFLAGS+= `pkg-config --libs opencv` 
COMMON+= `pkg-config --cflags opencv` 
endif

ifeq ($(GPU), 1) 
COMMON+= -DGPU -I/usr/local/cuda/include/
CFLAGS+= -DGPU
LDFLAGS+= -L/usr/local/cuda/lib64 -lcuda -lcudart -lcublas -lcurand
endif

ifeq ($(CUDNN), 1) 
COMMON+= -DCUDNN 
CFLAGS+= -DCUDNN
LDFLAGS+= -lcudnn
endif

OBJ=gemm.o utils.o cuda.o convolutional_layer.o list.o image.o activations.o im2col.o col2im.o blas.o crop_layer.o dropout_layer.o maxpool_layer.o softmax_layer.o data.o matrix.o network.o connected_layer.o cost_layer.o parser.o option_list.o darknet.o detection_layer.o captcha.o route_layer.o writing.o box.o nightmare.o normalization_layer.o avgpool_layer.o coco.o dice.o yolo.o yolt.o yolt2.o detector.o layer.o compare.o classifier.o local_layer.o swag.o shortcut_layer.o activation_layer.o rnn_layer.o gru_layer.o rnn.o rnn_vid.o crnn_layer.o demo.o tag.o cifar.o go.o batchnorm_layer.o art.o region_layer.o reorg_layer.o super.o voxel.o tree.o
ifeq ($(GPU), 1) 
LDFLAGS+= -lstdc++ 
OBJ+=convolutional_kernels.o activation_kernels.o im2col_kernels.o col2im_kernels.o blas_kernels.o crop_layer_kernels.o dropout_layer_kernels.o maxpool_layer_kernels.o network_kernels.o avgpool_layer_kernels.o
endif

OBJS = $(addprefix $(OBJDIR), $(OBJ))
DEPS = $(wildcard src/*.h) Makefile

all: obj results $(EXEC)
#all: obj backup results $(EXEC)


$(EXEC): $(OBJS)
	$(CC) $^ -o $@ $(COMMON) $(CFLAGS) $(LDFLAGS)

$(OBJDIR)%.o: %.c $(DEPS)
	$(CC) -c $< -o $@ $(COMMON) $(CFLAGS)

$(OBJDIR)%.o: %.cu $(DEPS)
	$(NVCC) $(ARCH) -c $< -o $@ $(COMMON) --compiler-options "$(CFLAGS)"

obj:
	mkdir -p obj
#backup:
#	mkdir -p backup
results:
	mkdir -p results

.PHONY: clean

clean:
	rm -rf $(OBJS) $(EXEC)

