##############################################################
#
#		Makefile to generate the Tango test server
#
##############################################################

#
# Get BIN DIR
#

ifndef ubuntu
OS=$(shell /csadmin/common/scripts/get_os.share)
endif

ifdef ubuntu
ifdef dev
OS=ubuntu1404
else
OS=ubuntu1204
endif
endif

BIN_DIR = $(OS)


##############################################################################
#
# 		Define base directory
#
##############################################################################

ifdef prefix
TANGO_HOME = $(prefix)/$(BIN_DIR)
LIB_TANGO_DIR = $(TANGO_HOME)/lib
ifdef shared
BIN_TANGO_DIR = $(TANGO_HOME)/bin/shared
else
BIN_TANGO_DIR = $(TANGO_HOME)/bin/archive
endif
else
#TANGO_HOME = /home/taurel/tango/cppapi_develop
TANGO_HOME = /home/taurel/tango/cppapi_900
LIB_TANGO_DIR = $(TANGO_HOME)/../install/$(BIN_DIR)/lib/debug
BIN_TANGO_DIR = bin/$(BIN_DIR)
endif

##############################################################################
#
# 		OmniORB base directory
#
##############################################################################

ifdef prefix
OMNI_BASE = /segfs/tango/ORB/omniORB4.2.1/$(BIN_DIR)
else
OMNI_BASE = /segfs/tango/ORB/omniORB4.2.1/$(BIN_DIR)
endif

##############################################################################
#
# 		Log4tango base directory
#
##############################################################################

ifdef prefix
LOG4TANGO_BASE = /segfs/tango/tools/log4tango-release/log4tango5.0.1/$(BIN_DIR)
else
LOG4TANGO_BASE = /segfs/tango/tools/log4tango-release/log4tango5.0.1/$(BIN_DIR)
endif

##############################################################################
#
# 		ZMQ base directory
#
##############################################################################


ifdef prefix
#ZMQ_BASE = /segfs/tango/transport/zmq/$(BIN_DIR)
ZMQ_BASE = /segfs/tango/transport/zmq4.0.5/$(BIN_DIR)
else
#ZMQ_BASE = /segfs/tango/transport/zmq/$(BIN_DIR)
ZMQ_BASE = /segfs/tango/transport/zmq4.0.5/$(BIN_DIR)
#ZMQ_BASE = /segfs/tango/transport/zmq4.1.3/$(BIN_DIR)
endif

ifdef linux
CC = c++
#CC = /home/taurel/bin/gcc-5.2/bin/c++5.2
AR = ar
endif


##############################################################################
#
# 		Compilation
#
##############################################################################

OBJS_DIR = 	objs
OBJS_FWD_DIR = fwd_ds/objs

ifdef prefix
INCLUDE_DIRS = -I$(OMNI_BASE)/include \
			   -I$(TANGO_HOME)/include \
			   -I$(LOG4TANGO_BASE)/include/tango \
			   -I$(ZMQ_BASE)/include \
			   -I. \
			   -I fwd_ds

LIB_DIRS = -L $(OMNI_BASE)/lib \
           -L $(LIB_TANGO_DIR)/debug \
	   	   -L $(LOG4TANGO_BASE)/lib \
		   -L $(ZMQ_BASE)/lib
else
INCLUDE_DIRS = -I$(OMNI_BASE)/include \
			   -I$(TANGO_HOME)/server \
			   -I$(TANGO_HOME)/client \
			   -I$(LOG4TANGO_BASE)/include/tango \
			   -I$(ZMQ_BASE)/include \
			   -I. \
			   -I fwd_ds

LIB_DIRS = -L $(OMNI_BASE)/lib \
           -L $(LIB_TANGO_DIR) \
           -L $(LOG4TANGO_BASE)/lib \
           -L $(ZMQ_BASE)/lib
endif


ifdef prefix
ifdef shared
LIB_TANGO = -ltango
else
LIB_TANGO = $(LIB_TANGO_DIR)/debug/libtango.a
endif
else
LIB_TANGO = -ltango
#LIB_TANGO = /home/taurel/tango/install/ubuntu1204/lib/debug/libtango.8.0.5.a -lgcov
endif


ifdef linux

#Check that we have at least gcc 4.3 (for c++0x features)

GCC_MAJOR_VERSION_GTEQ4 := $(shell expr `c++ -dumpversion | cut -f1 -d.` \>= 4)
GCC_MINOR_VERSION_GTEQ3 := $(shell expr `c++ -dumpversion | cut -f2 -d.` \>= 3)
ifeq ($(GCC_MAJOR_VERSION_GTEQ4),1)
    ifeq ($(GCC_MINOR_VERSION_GTEQ3),1)
        CXX11 = -std=c++0x
    endif
endif

CXXFLAGS =  -g $(CXX11) -D_REENTRANT $(INCLUDE_DIRS)
LFLAGS =  -g $(LIB_DIRS) $(LIB_TANGO) \
	-llog4tango -lomniORB4  -lomniDynamic4 \
	-lCOS4 -lomnithread -lzmq -lpthread -ldl
endif


SVC_OBJS = 	$(OBJS_DIR)/main.o \
		$(OBJS_DIR)/TypeCmds.o \
		$(OBJS_DIR)/SigThrow.o \
		$(OBJS_DIR)/IOMisc.o \
		$(OBJS_DIR)/IOStr1.o \
		$(OBJS_DIR)/IOStr2.o \
		$(OBJS_DIR)/IOArray1.o \
		$(OBJS_DIR)/IOArray2.o \
		$(OBJS_DIR)/IOStrArray.o \
		$(OBJS_DIR)/IOStruct.o \
		$(OBJS_DIR)/IOSetAttr.o \
		$(OBJS_DIR)/IOAddAttribute.o \
		$(OBJS_DIR)/IOSeqVec.o \
		$(OBJS_DIR)/FileDb.o \
		$(OBJS_DIR)/classfactory.o \
		$(OBJS_DIR)/DevTestClass.o \
		$(OBJS_DIR)/DevTest.o

SVC_FWD_OBJS = $(OBJS_FWD_DIR)/main.o \
			   $(OBJS_FWD_DIR)/ClassFactory.o \
			   $(OBJS_FWD_DIR)/FwdTestClass.o \
			   $(OBJS_FWD_DIR)/FwdTest.o \
			   $(OBJS_FWD_DIR)/FwdTestStateMachine.o

#.SUFFIXES:	.o .cpp
#.cpp.o:
#	@./cr_dir $(OBJS_DIR)
#	$(CC) $(CXXFLAGS) -c $< -o $(OBJS_DIR)/$*.o


$(OBJS_DIR)/%.o: %.cpp
	@./cr_dir $(OBJS_DIR)
	$(CC) $(CXXFLAGS) -c $< -o $(OBJS_DIR)/$*.o

$(OBJS_FWD_DIR)/%.o: fwd_ds/%.cpp
	@./cr_dir $(OBJS_FWD_DIR)
	$(CC) $(CXXFLAGS) -c $< -o $(OBJS_FWD_DIR)/$*.o

all: DevTest FwdTest

DevTest:	$(SVC_OBJS)
	@./cr_dir $(BIN_TANGO_DIR)
	$(CC) $(SVC_OBJS) -o $(BIN_TANGO_DIR)/devTest $(LFLAGS)

FwdTest:	$(SVC_FWD_OBJS)
	@./cr_dir $(BIN_TANGO_DIR)
	$(CC) $(SVC_FWD_OBJS) -o $(BIN_TANGO_DIR)/FwdTest $(LFLAGS)

clean:
	rm -f $(OBJS_DIR)/*.o $(OBJS_FWD_DIR)/*.o core






