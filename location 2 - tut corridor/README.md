# TUT Corridor Dataset

This directory contains various datasets collected in the TUT corridor environment for indoor localization and computer vision research. The TUT corridor is a controlled indoor environment measuring 2.02m × 28.7m, ideal for systematic data collection and algorithm validation.

## Dataset Overview

**Location**: TUT Corridor  
**Environment**: Indoor corridor (2.02m × 28.7m)  
**Technologies**: 
- Bluetooth Low Energy (BLE) beacon positioning
- ArUco marker-based visual positioning
- Battery performance analysis
- Distance-based visual marker detection

## Directory Structure

```
location 2 - tut corridor/
├── README.md                  # This documentation file
├── beacon_battery/            # BLE beacon battery performance analysis
│   ├── april_beacon/         # April Beacon samples with different battery states
│   └── tut_okabe_beacon/     # TUT Okabe Beacon samples with different battery states
├── distance/                  # ArUco marker visual detection at various distances
│   ├── aruco173/             # 173mm ArUco markers (1-30m distance samples)
│   ├── aruco273/             # 273mm ArUco markers (1-30m distance samples)
│   └── aruco385/             # 385mm ArUco markers (1-30m distance samples)
└── fingerprint/              # BLE RSSI fingerprinting dataset
    ├── README.md             # Detailed fingerprint dataset documentation
    ├── settings/             # Configuration files and marker definitions
    ├── offline/              # Static grid-based fingerprint data
    └── online/               # Dynamic ArUco-based trajectory data
```

## Dataset Descriptions

### Beacon Battery Analysis (`beacon_battery/`)

This dataset contains BLE beacon performance measurements under different battery conditions to analyze how battery degradation affects signal transmission characteristics.

**Collection Protocol**:
- **Distance**: 1.0 meter from beacon
- **Height**: 1.5 meter measurement height
- **Beacons Tested**: 
  - April Beacon
  - TUT Okabe Beacon
- **Battery States**: Multiple battery wear levels (new, 3 days, 3 months usage)

**Key Features**:
- Systematic battery performance comparison
- Controlled distance measurements for consistency
- Multiple beacon types for hardware variation analysis
- RSSI and transmission power monitoring across battery states

**Applications**:
- Battery life impact on localization accuracy
- Beacon maintenance scheduling optimization
- Signal degradation modeling
- Hardware reliability assessment

### Distance-Based ArUco Detection (`distance/`)

Visual ArUco marker detection dataset collected at systematic distances from 1 to 30 meters to evaluate marker detection performance and size optimization for various ranges.

**Marker Specifications**:
- **aruco173/**: 173mm (0.173m) ArUco markers
- **aruco273/**: 273mm (0.273m) ArUco markers  
- **aruco385/**: 385mm (0.385m) ArUco markers

**Collection Protocol**:
- **Distance Range**: 1-30 meters (1-meter increments)
- **File Formats**: 
  - `.matphoto`: OpenCV Mat data serialized as JSON and compressed with gzip
  - `.jpg`: Visual preview images for human inspection
- **Systematic Coverage**: Each marker size tested at all distances
- **Controlled Environment**: Consistent lighting and positioning

**Key Features**:
- Comprehensive distance-performance analysis
- Multiple marker sizes for range optimization
- Dual data formats (processing + preview)
- Systematic 1-meter distance increments
- 30 samples per marker size (900 total samples)

**File Naming Convention**:
```
{distance_meter}.matphoto  # OpenCV data for processing
{distance_meter}.jpg       # Visual preview
```

**Applications**:
- Optimal marker size selection for target distances
- Detection algorithm performance evaluation
- Camera calibration validation
- Visual positioning system design
- Real-time detection threshold optimization

### BLE Fingerprinting (`fingerprint/`)

Comprehensive Bluetooth Low Energy fingerprinting dataset for indoor localization research, including both static grid-based measurements and dynamic trajectory-based data collection with ArUco ground truth.

**Key Features**:
- Static offline measurements (171 grid positions)
- Dynamic online trajectories with visual positioning
- Multiple collection approaches and environmental conditions
- Extensive ArUco marker integration for ground truth validation

**See**: [`fingerprint/README.md`](fingerprint/README.md) for comprehensive documentation

## Coordinate System

**TUT Corridor Reference Frame**:
- **Origin (0,0)**: Corridor corner
- **X-axis**: Corridor width (0-2.02m)
- **Y-axis**: Corridor length (0-28.7m)  
- **Z-axis**: Height above floor (meters)

This coordinate system is consistent across all datasets in this location.

## Data Formats

### Beacon Battery Dataset
CSV format with timestamp, RSSI, transmission power, and battery state information:

| Column        | Type    | Description                           | Unit   |
|---------------|---------|---------------------------------------|--------|
| `timestamp`   | int64   | Unix timestamp (milliseconds)        | ms     |
| `time`        | string  | Human-readable timestamp              | -      |
| `mac_address` | string  | Beacon MAC address                    | -      |
| `rssi`        | int     | Received Signal Strength Indicator   | dBm    |
| `tx_power`    | int     | Transmission power                    | dBm    |
| `battery_age` | string  | Battery usage duration                | -      |

### Distance Dataset
Two formats per distance measurement:

**MatPhoto Format** (`.matphoto`):
- OpenCV Mat data serialized as JSON
- Compressed using gzip for storage efficiency
- Contains full image matrix for processing
- Preserves all pixel data and metadata

**Preview Format** (`.jpg`):
- Standard JPEG image for visual inspection
- Human-readable format
- Quality optimized for file size
- Suitable for documentation and validation

### Fingerprint Dataset
Multiple CSV formats depending on measurement type (offline/online/static/trajectory). 

**See**: [`fingerprint/README.md`](fingerprint/README.md) for detailed format specifications.

## Equipment and Collection Setup

### Hardware Used
- **Smartphones**: Android devices with BLE scanning capabilities
- **Beacons**: April Beacon, TUT Okabe Beacon (various battery states)
- **Cameras**: Smartphone cameras for ArUco detection
- **Measurement Tools**: Laser meter for precise positioning
- **Mounting**: Tripods and chest harnesses for consistent positioning

### Collection Environment
- **Location**: TUT Corridor - controlled indoor environment
- **Dimensions**: 2.02m × 28.7m rectangular corridor
- **Lighting**: Consistent indoor fluorescent lighting
- **Interference**: Typical office environment WiFi and BLE background

## Processing and Analysis Tools

The main repository includes various tools for data processing and analysis:

### Common Tools (`tools/`)
- `common_tools.py`: Utility functions for data loading
- `trajectory_plotter.py`: Visualization tools for spatial data
- `rssi_dataset_checker.py`: Data validation and consistency checks
- `csv_to_json_markers.py`: ArUco marker format conversion

### Usage Examples

**Battery Performance Analysis**:
```python
# Load battery comparison data
import pandas as pd
new_battery = pd.read_csv("beacon_battery/april_beacon/new_battery.csv")
aged_battery = pd.read_csv("beacon_battery/april_beacon/3_months_battery.csv")

# Compare RSSI distributions
rssi_new = new_battery['rssi'].mean()
rssi_aged = aged_battery['rssi'].mean()
print(f"RSSI degradation: {rssi_new - rssi_aged} dBm")
```

**Distance Detection Analysis**:
```python
# Analyze detection success rate by distance and marker size
import gzip, json
import cv2 as cv

# Load MatPhoto data
with gzip.open("distance/aruco173/15.matphoto", 'rt') as f:
    mat_data = json.load(f)
    image = cv.imdecode(np.array(mat_data), cv.IMREAD_COLOR)
    
# Process for ArUco detection
# (Add your ArUco detection pipeline here)
```

## Known Issues and Limitations

### Beacon Battery Dataset
- **Environmental Variation**: Slight temperature and humidity changes between sessions
- **Battery Aging**: Non-linear degradation may not be perfectly captured
- **Device Differences**: Beacon hardware variations may affect baseline performance
- **Limited Sample Size**: Only two beacon types tested

### Distance Dataset
- **Lighting Dependency**: Detection performance varies with ambient lighting
- **Perspective Effects**: Marker orientation may affect detection at extreme distances
- **Camera Quality**: Smartphone camera limitations at maximum distances
- **Compression**: JPEG preview quality may not represent full detection capability

### General Limitations
- **Single Location**: Data collected only in TUT corridor environment
- **Device Specificity**: Android smartphone hardware variations
- **Time-of-Day Effects**: Collection performed during specific time periods
- **Environmental Control**: Limited control over external interference

## Applications and Use Cases

### Research Applications
- **Indoor Localization**: BLE fingerprinting algorithm development
- **Visual Positioning**: ArUco-based positioning system validation
- **Sensor Fusion**: Combining BLE and visual positioning approaches
- **Battery Management**: Beacon maintenance optimization
- **Computer Vision**: Marker detection algorithm evaluation
- **Distance Estimation**: Visual ranging system development

### Algorithm Development
- **Machine Learning**: Training datasets for localization algorithms
- **Signal Processing**: RSSI filtering and processing techniques
- **Image Processing**: ArUco detection optimization
- **Sensor Calibration**: Multi-modal positioning system alignment

### Benchmarking
- **Performance Comparison**: Standardized datasets for algorithm comparison
- **Hardware Evaluation**: Beacon and camera performance assessment
- **Range Analysis**: Distance-based detection capability evaluation

## Citation and Usage

If you use this dataset in your research, please cite:

```bibtex
@dataset{tut_corridor_dataset_2025,
  title={TUT Corridor Multi-Modal Indoor Localization Dataset},
  author={[Author Names]},
  year={2025},
  institution={[Institution Name]},
  location={TUT Corridor},
  note={BLE fingerprinting, ArUco detection, and battery analysis dataset}
}
```

## Contact Information

For questions, issues, or additional information about this dataset:

- **Dataset Maintainer**: [Your Name]
- **Institution**: [Your Institution]  
- **Email**: [your.email@domain.com]
- **Repository**: [GitHub Repository URL]

## Version History

- **v2.1** (2025-12-05): Complete dataset with all three components
  - Fingerprint dataset Take 2 enhanced collection
  - Distance dataset with three marker sizes (173mm, 273mm, 385mm)
  - Beacon battery analysis for two beacon types
  - Comprehensive documentation and processing tools

- **v2.0** (2025-11-21): Added fingerprint online dataset Take 1
  - Dynamic trajectory measurements with ArUco positioning
  - Static dual-ground-truth measurements
  - Enhanced marker configurations

- **v1.5** (2025-10-15): Added distance and battery datasets  
  - Systematic distance measurements (1-30m)
  - Battery performance analysis across usage periods
  - Multiple ArUco marker sizes evaluation

- **v1.0** (2025-10-07): Initial fingerprint offline dataset
  - Grid-based static measurements
  - 5-beacon configuration
  - Basic validation tools

## License

This dataset is released under [specify license, e.g., CC BY 4.0, MIT, etc.].

---

**Last Updated**: January 5, 2026  
**Dataset Version**: 2.1  
**Collection Period**: October - December 2025  
**Total Data Size**: ~15+ GB (including all subdatasets)
- Beacon Battery: ~50 MB
- Distance: ~3 GB  
- Fingerprint: ~10.4+ GB
- Processing Tools: ~10 MB