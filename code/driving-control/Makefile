# Чтение настроек из boards.txt
BOARD = uno
include boards.txt

# Автоматическое определение настроек на основе выбранной платы
MCU = $(uno.build.mcu)
F_CPU = $(uno.build.f_cpu)
BOARD_NAME = $(uno.name)
UPLOAD_SPEED = $(uno.upload.speed)
UPLOAD_PROTOCOL = $(uno.upload.protocol)

# Настройки компилятора
CC = avr-gcc
CXX = avr-g++
OBJCOPY = avr-objcopy
AVRDUDE = avrdude
SIZE = avr-size

# Порт Arduino (автоопределение для Linux/Mac)
UNAME_S := $(shell uname -s)
ifeq ($(UNAME_S),Linux)
    PORT ?= /dev/ttyUSB0
else ifeq ($(UNAME_S),Darwin)
    PORT ?= /dev/tty.usbmodem*
else
    PORT ?= COM3
endif

# Флаги компиляции
CFLAGS = -mmcu=$(MCU) -DF_CPU=$(F_CPU) -Os -Wall
CXXFLAGS = $(CFLAGS) -std=c++17

# Источники
SOURCES = $(wildcard src/*.cpp)
OBJECTS = $(SOURCES:.cpp=.o)
TARGET = firmware

# Основная цель
all: $(TARGET).hex

$(TARGET).elf: $(OBJECTS)
	$(CXX) $(CXXFLAGS) -o $@ $^
	@echo "=== Размер памяти ==="
	$(SIZE) $@

$(TARGET).hex: $(TARGET).elf
	$(OBJCOPY) -O ihex -R .eeprom $< $@

%.o: %.cpp
	$(CXX) $(CXXFLAGS) -c $< -o $@

upload: $(TARGET).hex
	@echo "Прошивка $(BOARD_NAME) на порту $(PORT)"
	$(AVRDUDE) -p $(MCU) -c $(UPLOAD_PROTOCOL) -P $(PORT) -b $(UPLOAD_SPEED) -D -U flash:w:$<

clean:
	rm -f $(OBJECTS) $(TARGET).elf $(TARGET).hex

monitor:
	screen $(PORT) 9600

# Дополнительные цели
list-boards:
	@echo "Доступные платы:"
	@echo "- uno: Arduino Uno"
	@echo "- nano: Arduino Nano" 
	@echo "- mega: Arduino Mega 2560"

set-board:
	$(eval BOARD = $(filter-out $@,$(MAKECMDGOALS)))
	@echo "Установлена плата: $(BOARD)"

.PHONY: all upload clean monitor list-boards set-board