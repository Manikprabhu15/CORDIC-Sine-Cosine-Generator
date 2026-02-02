# QuestaSim commands
VLOG = vlog
VSIM = vsim

# Directories
RTL_DIR = ..\rtl
TB_DIR = ..\tb
TEST_DIR = ..\test
AGENT_DIR = ..\agent
LOG_DIR = logs
WORK_DIR = work

# File names
PKG_FILE = $(TEST_DIR)\pkg.sv
TOP_FILE = $(TB_DIR)\cordic_top.sv

# Top module name
TOP_MODULE = cordic_top

# Test name
TEST = std_ip_test

UVM_HOME = C:\questasim_10.1d\verilog_src\questa_uvm_pkg-1.2

# Compilation
VLOG_FLAGS = -work $(WORK_DIR) +acc -sv
VLOG_FLAGS += +incdir+$(UVM_HOME)\src
VLOG_FLAGS += +incdir+$(RTL_DIR)
VLOG_FLAGS += +incdir+$(TB_DIR)
VLOG_FLAGS += +incdir+$(TEST_DIR)
VLOG_FLAGS += +incdir+$(AGENT_DIR)
VLOG_FLAGS += $(UVM_HOME)\src\questa_uvm_pkg.sv

# Simulation
VSIM_FLAGS = -c
VSIM_FLAGS += +UVM_TESTNAME=$(TEST)
VSIM_FLAGS += -do "run -all; quit -f"

.PHONY: help setup compile simulate all clean check_uvm

# Default target
help:
	@echo ====================================
	@echo QuestaSim UVM Verification Makefile
	@echo ====================================
	@echo Available targets:
	@echo   make help      - Show this help message
	@echo   make check_uvm - Check UVM library path
	@echo   make setup     - Create work library and logs directory
	@echo   make compile_rtl   - Compile rtl
	@echo   make compile   - Compile PKG and TOP files
	@echo   make simulate  - Run simulation
	@echo   make all       - Setup, compile and simulate
	@echo   make clean     - Remove generated files and logs
	@echo ====================================
	@echo Current Settings:
	@echo   UVM_HOME   = $(UVM_HOME)
	@echo   PKG_FILE   = $(PKG_FILE)
	@echo   TOP_FILE   = $(TOP_FILE)
	@echo   TOP_MODULE = $(TOP_MODULE)
	@echo   TEST       = $(TEST)
	@echo ====================================
	@echo IMPORTANT: Update UVM_HOME in Makefile to your QuestaSim path!
	@echo Run 'make check_uvm' to verify UVM library path
	@echo ====================================

# Check if UVM library exists(optional)
check_uvm:
	@echo Checking UVM library path...
	@echo UVM_HOME = $(UVM_HOME)
	@if exist "$(UVM_HOME)\src\questa_uvm_pkg.sv" (echo [OK] UVM library found!) else (echo [ERROR] UVM library NOT found! && echo Please update UVM_HOME in Makefile to point to your QuestaSim UVM library && echo Common locations: && echo   C:\modeltech64_10.1d\verilog_src\uvm-1.2 && echo   C:\questasim_10.1d\verilog_src\uvm-1.2 && echo   C:\intelFPGA\20.1\modelsim_ase\verilog_src\uvm-1.2)

# Create work library and logs directory(optional)
setup:
	@if not exist $(WORK_DIR) vlib $(WORK_DIR)
	@if not exist $(LOG_DIR) mkdir $(LOG_DIR)
	@echo [SETUP] Work library and logs directory ready


# Compile RTL files
compile_rtl: setup
	@echo ====================================
	@echo Compiling RTL files from $(RTL_DIR)...
	@echo ====================================
	@echo RTL files to compile:
	@for %%f in ($(RTL_DIR)\*.v) do @echo   %%~nxf
	@for %%f in ($(RTL_DIR)\*.sv) do @echo   %%~nxf
	@echo.
	@echo Compiling RTL...
	@(for %%f in ($(RTL_DIR)\*.v) do @$(VLOG) $(VLOG_FLAGS) "%%f") > $(LOG_DIR)\compile_rtl.log 2>&1
	@(for %%f in ($(RTL_DIR)\*.sv) do @$(VLOG) $(VLOG_FLAGS) "%%f") >> $(LOG_DIR)\compile_rtl.log 2>&1
	@if errorlevel 1 (echo RTL Compilation FAILED! && type $(LOG_DIR)\compile_rtl.log && exit 1) else (echo RTL Compilation SUCCESS!)

# Compile PKG file (which includes all other files) and then TOP
compile: setup
	@if not exist "$(UVM_HOME)\src\questa_uvm_pkg.sv" (echo [ERROR] UVM library NOT found at $(UVM_HOME) && echo Please update UVM_HOME in Makefile && exit 1)
	@$(VLOG) $(VLOG_FLAGS) "$(PKG_FILE)" > $(LOG_DIR)\compile.log 2>&1 || (echo [FAILED] PKG compilation failed! && echo. && echo Error Log: && type $(LOG_DIR)\compile.log && exit 1)
	@$(VLOG) $(VLOG_FLAGS) "$(TOP_FILE)" >> $(LOG_DIR)\compile.log 2>&1 || (echo [FAILED] TOP compilation failed! && echo. && echo Error Log: && type $(LOG_DIR)\compile.log && exit 1)
	@echo ====================================
	@echo Compilation Complete!
	@echo ====================================

# Run simulation
simulate:
	@echo ====================================
	@echo Running UVM Simulation...
	@echo Top Module: $(TOP_MODULE)
	@echo Test Name:  $(TEST)
	@echo ====================================
	@$(VSIM) $(VSIM_FLAGS) -l $(LOG_DIR)\simulate.log $(WORK_DIR).$(TOP_MODULE)
	@echo ====================================
	@echo Simulation Complete!
	@echo Check $(LOG_DIR)\simulate.log for details
	@echo ====================================

# Compile and simulate
all: setup compile simulate

# Clean generated files
clean:
	@echo Cleaning up...
	@if exist $(WORK_DIR) rmdir /S /Q $(WORK_DIR)
	@if exist $(LOG_DIR) rmdir /S /Q $(LOG_DIR)
	@if exist transcript del /Q transcript
	@if exist vsim.wlf del /Q vsim.wlf
	@if exist *.wlf del /Q *.wlf
	@echo Clean complete!

.DEFAULT_GOAL := help
