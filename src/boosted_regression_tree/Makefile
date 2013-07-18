#------------------------------------------------------------------------------
# Makefile for sample HDF code
#------------------------------------------------------------------------------

# Set up compile options
CXX = g++
RM = rm -f
EXTRA = -Wall -g

# Define the include files
INC = const.h date.h error.h input.h myhdf.h mystring.h output.h predict.h \
      PredictBurnedArea.h
INCDIR  = -I. -I$(HDFINC) -I$(HDFEOS_INC) -I$(OPENCVINC) -I$(BOOST_INC)
NCFLAGS = $(EXTRA) $(INCDIR)

# Define the source code and object files
SRC = date.cpp \
      error.cpp \
      FileIO.cpp \
      input.cpp \
      myhdf.cpp \
      mystring.cpp \
      output.cpp \
      predict.cpp \
      PredictBurnedArea.cpp \
      predict_burned_area.cpp

OBJ = $(SRC:.cpp=.o)

# Define the object libraries
#LIB   = -L$(HDFLIB) -lmfhdf -ldf -lxdr -L$(JPEGLIB) -ljpeg \
#        -L$(ZLIBLIB) -lz -L$(SZIPLIB) -lsz -lm
#EOSLIB = -L$(HDFEOS_LIB) -lhdfeos -L$(HDFEOS_GCTPLIB) -lGctp

LIB   = -L$(BOOST_LIB) -lboost_program_options -L$(GDAL_LIB) -lgdal \
        -L$(HDFEOS_LIB) -lhdfeos -L$(HDFLIB) -lmfhdf -ldf -lxdr \
        -L$(JPEGLIB) -ljpeg -L$(TIFFLIB) -ltiff -ltiffxx \
        -L$(OPENCVLIB) -lopencv_ml -lopencv_highgui -lopencv_core

# Define the executable
EXE = predict_burned_area

# Target for the executable
all: $(EXE)

predict_burned_area: $(OBJ) $(INC)
#	$(CXX) $(EXTRA) -o $(EXE) -dynamic-linker /usr/lib/debug/usr/lib/x86_64-linux-gnu/crt1.o /usr/lib/debug/usr/lib/x86_64-linux-gnu/crti.o $(OBJ) $(LIB) /usr/lib/debug/usr/lib/x86_64-linux-gnu/crtn.o
	$(CXX) $(EXTRA) -o $(EXE) $(OBJ) $(LIB)

install:
	cp $(EXE) $(BIN)
	cp ../../scripts/boosted_regression_tree/*.py $(BIN)

clean:
	$(RM) $(OBJ) $(EXE)

$(OBJ): $(INC)

.cpp.o:
	$(CXX) $(NCFLAGS) -c $<
