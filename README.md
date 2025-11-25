# Learning to See in the Dark (Lite Demo)

This MATLAB script demonstrates low-light image enhancement using a pretrained Deep Learning network. It is based on the "Learning to See in the Dark" (SID) architecture.

Instead of downloading the massive SID dataset and training from scratch, this demo downloads a lightweight pretrained model and simulates low-light conditions on a standard image to test the network's capabilities.

## 🖼️ Results

> <img width="1045" height="314" alt="Result" src="https://github.com/user-attachments/assets/97a681b9-1a0f-450a-8a64-e1dc48b840a9" />
>
> *Left: Original | Center: Simulated Dark Input | Right: Network Output*

## ⚙️ Code Sections

The script operates in a linear pipeline consisting of four main stages:

### 1\. Model Acquisition

The script checks for the existence of the pretrained network (`trainedLowLightCameraPipelineNet.mat`) in a temporary directory.

  * If the model is missing, it automatically downloads the `.zip` from the official MathWorks support repository and extracts it.
  * This ensures the script is portable and requires no manual dependency setup.

### 2\. Image Loading & Simulation

The script reads a standard RGB image (`PXL.jpg`) and resizes it to $512 \times 512$. It then artificially degrades the image to simulate a poor capture environment:

  * **Darkening:** Reduces the intensity of the pixels.
  * **Noise:** Adds Gaussian noise to simulate sensor grain in low light.

### 3\. "Fake RAW" Preprocessing

The pretrained network expects RAW camera data (Bayer pattern) as input. Since we are using a standard JPEG:

  * The image is converted to grayscale.
  * It is replicated into 4 channels to mimic the dimensions of RAW sensor data ($H \times W \times 4$).
  * The data is formatted into a `dlarray` with **SSCB** dimensions (Spatial, Spatial, Channel, Batch).

### 4\. Inference & Visualization

  * The data is passed through the `netTrained` network.
  * The output is converted back to a standard `uint8` image.
  * A comparison figure is generated showing the Original, the Low-Light Simulation, and the Enhanced Output.

-----

## 🔧 Configuration Changes

A specific adjustment was made to the light simulation parameters to better suit the input image.

### `darkFactor` Adjustment

  * **Previous Value:** `0.03`
  * **New Value:** `0.5`

**Reason for change:**
The original factor of `0.03` resulted in an input image that was nearly pitch black, causing the specific input image `PXL.jpg` to lose too much structural information before the noise was even applied. Increasing the factor to `0.5` simulates a dim environment rather than total darkness, allowing for a more accurate evaluation of the network's denoising and color restoration capabilities on this specific sample.

```matlab
%% 4) Simulate a very dark, noisy low-light image
darkFactor = 0.5;        % <--- MODIFIED: Increased from 0.03 to 0.5
I_dark = I * darkFactor; % Reduce brightness
```

## 🚀 Usage

1.  Ensure you have **MATLAB** with the **Deep Learning Toolbox** and **Computer Vision Toolbox** installed.
2.  Place your input image (e.g., `PXL.jpg`) in the project folder.
3.  Run the script. The model will download automatically on the first run.

