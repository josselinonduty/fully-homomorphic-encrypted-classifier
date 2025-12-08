# Fully Homomorphic Encrypted Classifier

This project implements an encrypted image classifier using Fully Homomorphic Encryption (FHE), specifically the ResNet20 model on CIFAR-10 images. It leverages the [OpenFHE](https://github.com/openfheorg/openfhe-development) library and the [LowMemoryFHEResNet20](https://github.com/lorenzorovida/LowMemoryFHEResNet20) implementation.

## Prerequisites

Ensure you have the following installed on your system:

- **Git**
- **CMake** (version 3.5.1 or higher)
- **C++ Compiler** (g++ or clang)
- **Python 3** (for plain implementation comparisons)
  - Required Python packages: `torch`, `torchvision`, `PIL`, `numpy`

## Installation & Setup

This project consists of the main repository, the `LowMemoryFHEResNet20` submodule, and the `OpenFHE` library.

### 1. Clone the Repository

Clone this repository and initialize the submodules:

```bash
git clone --recurse-submodules <repository-url>
cd fully-homomorphic-encrypted-classifier
```

If you have already cloned the repository without submodules, run:

```bash
git submodule update --init --recursive
```

### 2. Download, Build, and Install OpenFHE

The project relies on the OpenFHE library. You need to download and install it first.

#### Download OpenFHE

If the `openfhe` directory is missing or empty, clone it from the official repository:

```bash
git clone https://github.com/openfheorg/openfhe-development.git openfhe

# https://github.com/openfheorg/openfhe-development/releases/tag/v1.0.4
cd openfhe
git checkout v1.0.4
cd ..
```

#### Build and Install

We will build OpenFHE and install it. By default, `make install` installs the library system-wide (usually to `/usr/local`).

```bash
cd openfhe
mkdir build
cd build
cmake ..
make -j$(nproc)
sudo make install
```

> **Note:** If you do not have sudo access or prefer a local installation, you can specify an install prefix:
>
> ```bash
> cmake -DCMAKE_INSTALL_PREFIX=../../openfhe-install ..
> make -j$(nproc)
> make install
> ```
>
> If you choose this method, you will need to tell CMake where to find OpenFHE when building the classifier (see below).

### 3. Build LowMemoryFHEResNet20

Now that OpenFHE is installed, you can build the classifier.

```bash
# Go back to the root of the project if you are in openfhe/build
cd ../..

cd LowMemoryFHEResNet20
mkdir build
cd build
```

Run CMake.

- If you installed OpenFHE system-wide (default):
  ```bash
  cmake ..
  ```
- If you installed OpenFHE locally (e.g., to `../../openfhe-install`):
  ```bash
  cmake -DCMAKE_PREFIX_PATH=../../openfhe-install ..
  ```

Finally, build the executable:

```bash
make -j$(nproc)
```

## Usage

After building, the executable `LowMemoryFHEResNet20` will be located in `LowMemoryFHEResNet20/build`.

### Running the Encrypted Inference

Navigate to the build directory:

```bash
cd LowMemoryFHEResNet20/build
```

Run the executable:

```bash
./LowMemoryFHEResNet20
```

### Custom Arguments

You can customize the execution with the following arguments:

- **Generate Keys**: Create new keys for a specific parameter set (1-4).

  ```bash
  ./LowMemoryFHEResNet20 generate_keys 1
  ```

  _This creates a `keys_exp1` folder in the project root._

- **Load Keys**: Use existing keys to run inference.

  ```bash
  ./LowMemoryFHEResNet20 load_keys 1
  ```

- **Custom Input Image**: Classify a specific image (must be 32x32 RGB).

  ```bash
  ./LowMemoryFHEResNet20 load_keys 1 input "inputs/vale.jpg"
  ```

- **Compare with Plain Model**: Run the Python script to compare encrypted results with unencrypted inference.
  ```bash
  ./LowMemoryFHEResNet20 load_keys 1 input "inputs/vale.jpg" plain
  ```

### Output Interpretation

The output is a vector of 10 scores. The index of the maximum value corresponds to the predicted class:

| Index | Class      |
| ----- | ---------- |
| 0     | Airplane   |
| 1     | Automobile |
| 2     | Bird       |
| 3     | Cat        |
| 4     | Deer       |
| 5     | Dog        |
| 6     | Frog       |
| 7     | Horse      |
| 8     | Ship       |
| 9     | Truck      |

## Troubleshooting

- **OpenFHE not found**: Ensure you ran `make install` for OpenFHE or provided the correct `CMAKE_PREFIX_PATH`.
- **DropLastElement Error**: If you encounter this error with newer OpenFHE versions, consider increasing the ring dimension or adjusting security parameters as noted in the LowMemoryFHEResNet20 documentation.
