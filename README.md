# Wireshark MQTT Data Analysis

A comprehensive project for analyzing MQTT protocol traffic captured via Wireshark. This tool processes packet capture files (`.pcapng`), extracts protocol-level data, converts hex payloads to ASCII, calculates buffer metrics, and generates visualization plots.

## 📋 Project Overview

This project analyzes MQTT network traffic across different devices (laptop, Raspberry Pi) with varying message intervals (10ms - 100ms). It extracts network metrics, processes MQTT payloads, and provides statistical analysis and visualization of network performance.

## 🎯 What It Does

### 1. **Packet Capture Processing** (`process_tshark.py`)
- Lists available `.pcapng` files from the `WiresharkData` directory
- Allows user selection of a specific capture file
- Uses `tshark` to extract network packet information including:
  - Frame timing and relative timestamps
  - Source/destination IP addresses
  - TCP sequence/acknowledgment numbers
  - MQTT message text and payload
  - Protocol information

### 2. **Hex to ASCII Conversion** (`HexToASCII.py`)
- Converts hexadecimal TCP payloads to ASCII text
- Processes the CSV output from tshark
- Adds an `ASCIIinfo` column with decoded MQTT message content
- Handles invalid hex data gracefully with error messages

### 3. **Buffer Length Calculation** (`calc_bufferlength_apmd.cpp` & `main.cpp`)
- Calculates buffer lengths using APMD (Adaptive Predictive Model Decomposition)
- Processes CSV data from the conversion stage
- Generates output CSV files with computed metrics

### 4. **Data Analysis & Visualization** (`plot.py`)
- Generates interactive plots for network metrics:
  - **Delay Analysis**: Line plot and optional box plot visualization
  - **Retransmission Metrics**: Number of retransmissions per signal
  - **Buffer Length Metrics**: With support for original and alternative definitions
- Extracts metadata from filenames to determine:
  - Message interval (10ms, 20ms, ... 100ms)
  - Device type (laptop or Raspberry Pi)
- Supports shared x-axis for comparative analysis

## 📁 Project Structure

```
Wireshark-MQTT-Data-Analysis/
├── README.md                          # This file
├── makefile                           # Build automation
├── main.cpp                           # Buffer length calculation (C++)
├── calc_bufferlength_apmd.cpp        # APMD-based buffer calculation (C++)
├── process_tshark.py                 # Tshark packet processing (Python)
├── HexToASCII.py                     # Hex to ASCII conversion (Python)
├── plot.py                           # Data visualization (Python)
├── acfq aefq.py                      # Additional analysis utilities
├── WiresharkData/                    # Captured MQTT traffic files (.pcapng)
├── WiresharkOldData/                 # Archive of older capture files
├── .gitignore                        # Git ignore rules
└── .gitattributes                    # Git attributes
```

## 🚀 Getting Started

### Prerequisites
- **Wireshark/tshark**: For packet capture analysis
  ```bash
  # macOS
  brew install wireshark
  
  # Ubuntu/Debian
  sudo apt-get install tshark
  
  # Fedora
  sudo dnf install wireshark
  ```

- **Python 3.6+**: For data processing and visualization
  ```bash
  pip install pandas matplotlib
  ```

- **C++ Compiler**: For buffer length calculations (g++ or clang)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/YOUR_USERNAME/Wireshark-MQTT-Data-Analysis.git
   cd Wireshark-MQTT-Data-Analysis
   ```

2. Install Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```
   (Or manually: `pip install pandas matplotlib`)

3. Compile C++ programs:
   ```bash
   make
   ```

## 📊 Usage Workflow

### Full Pipeline

1. **Start with MQTT Capture Files**
   - Place `.pcapng` files in the `WiresharkData/` directory
   - These contain raw MQTT traffic captured via Wireshark

2. **Process Packets**
   ```bash
   python3 process_tshark.py tshark_output.csv selected_pcapng.txt
   ```
   - Select a pcapng file from the menu
   - Extracts packet fields to CSV format

3. **Convert Hex Payloads to ASCII**
   ```bash
   python3 HexToASCII.py
   ```
   - Converts hexadecimal MQTT payloads to readable ASCII
   - Input: `tshark_output.csv`
   - Output: `Data_after_conversion.csv`

4. **Calculate Buffer Metrics**
   ```bash
   ./a.out
   ```
   - Processes converted data
   - Calculates buffer lengths using APMD
   - Input: `Data_after_conversion.csv`
   - Output: `output.csv`, `output2.csv`

5. **Generate Visualizations**
   ```bash
   python3 plot.py
   ```
   - Creates plots for delay, retransmissions, and buffer length
   - Options for box plots and alternative buffer definitions
   - Interactive matplotlib interface

### Individual Script Usage

Each Python script can be run independently if intermediate data files exist:

```bash
# Process specific packet capture
python3 process_tshark.py output.csv selected.txt

# Convert hex data only
python3 HexToASCII.py

# Plot analysis only (requires processed CSV files)
python3 plot.py
```

## 📈 Output Files

| File | Description |
|------|-------------|
| `tshark_output.csv` | Raw packet data extracted by tshark |
| `Data_after_conversion.csv` | ASCII-decoded MQTT payloads |
| `output.csv` | Primary buffer metrics |
| `output2.csv` | Alternative buffer calculations |
| `selected_pcapng.txt` | Path to currently selected packet capture file |

## 🛠️ Build System

The `makefile` provides automated build and execution:

```bash
make              # Compile C++ programs
make run          # Run full pipeline
make clean        # Remove compiled files and outputs
make help         # Show available targets
```

## 📝 Data Analysis Parameters

### Extracted Metrics
- **Delay**: Time between packet transmission and acknowledgment
- **Retransmissions**: Number of TCP retransmissions per signal
- **Buffer Length**: Computed using Adaptive Predictive Model Decomposition
- **Signal Number**: Sequential identifier for MQTT messages
- **Device Type**: Laptop or Raspberry Pi
- **Message Interval**: 10ms to 100ms intervals

### Test Scenarios
- Different message intervals (10ms, 20ms, 30ms, ... 100ms)
- Multiple devices (laptop and Raspberry Pi)
- Varying network conditions captured in separate files

## 🔧 Customization

### Modifying tshark Fields
Edit `process_tshark.py` to extract different packet fields:
```python
tshark_cmd = [
    "tshark", "-r", selected_pcapng_path, "-T", "fields",
    "-e", "frame.time_relative",  # Add/remove fields here
    "-e", "ip.src", "-e", "ip.dst",
    # ... more fields
]
```

### Changing Plot Definitions
`plot.py` supports multiple buffer length definitions. Choose during runtime:
```
Do you follow the original definition of buffer length? (1/0):
```

## 📦 Dependencies

- **tshark**: Wireshark's command-line packet analyzer
- **pandas**: Data manipulation and CSV processing
- **matplotlib**: Data visualization
- **Python 3.6+**: Scripting runtime
- **C++11 or later**: For compilation


