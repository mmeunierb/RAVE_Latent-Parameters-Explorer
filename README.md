# RAVE Latent Parameters Explorer

This device encourages the exploration of RAVE models' latent parameters by manually dialing in or randomizing the connections between the encoder and decoder, the value of each latent dimension, and their modulation amount.  

It is based on the Acids Lab's work (RAVE and `nn~`), and is essentially a redesign of the [RAVE VST version](https://forum.ircam.fr/projects/detail/rave-vst/), with quicker access to and a simpler visual representation of the latent connections and parameters.

![latent-params](assets/rave-params-exp.png)

# Installation

1. **Download the latest `nn~` version** [here](https://github.com/domkirke/nn_tilde/releases/), extract it, and place it into your Max `Packages` folder.

2. **FOR WINDOWS USERS**:  
   As instructed on the `nn~` [web page](https://forum.ircam.fr/projects/detail/nn/), copy and paste the `.dll` files from the package next to the `Max.exe` (usually in `C:\Program Files\Cycling '74\Max 8`).

3. **Download pre-trained RAVE models** from the [Acids Lab](https://acids-ircam.github.io/rave_models_download) or [Intelligent Instruments Lab](https://huggingface.co/Intelligent-Instruments-Lab/rave-models) and place them in a designated folder.

   If you already have a RAVE models folder, make sure that the path is added to `Options > File Preferences` in Max. This is necessary for the dynamic loading of models.  

   Note that the patch’s `Browse` button lets you manually choose a custom location to replace the default path of the dropdown menu.

# Usage

## Before launching the `.maxproj`

### Testing nn~
After setting a file path to the RAVE models, an easy way to make sure they are properly detected by Max is to open a new patcher, create a `nn~` object with a model name (e.g., `isis.ts`) and a method (`forward`, `encode`, or `decode`) as arguments: e.g., `nn~ isis.ts forward`

  If the file path and the `.dll` files (Windows) are properly detected, the object will load without any error messages.  

  If an error message appears in the Max console, either the RAVE models folder or the `.dll` files (Windows) are misplaced.

### Audio Status
Before opening `RAVE_params_explorer.maxproj`, launch Max, go to `Audio Status`, and select the desired input and output devices. This ensures the proper loading of the patch and prevents any issues related to audio settings.  

  **FOR WINDOWS USERS**:
  Since the device loads four instances of the models inside two `mcs.nn~` objects, it is recommended to set the audio driver to `ad_mme`, the `Thread Priority` to **Highest**, and `Latency (ms)` to **250**.  

  Other set-ups can cause CPU spikes and major audio drops (tested under Windows 10, ThinkPad T14 Gen5 AMD Ryzen 8840u, 32GB RAM).

---
## Getting started

On startup, the first model in the dropdown menu will be loaded and heard.

### Loading models 

Technically, there is no need to change the file path in the dropdown menu; if the RAVE models folder is properly seen by Max and the same models are present, they will load correctly in the `nn~` objects.  

  However, to ensure that the dropdown menu reflects the models present on your device, it is better to change the path to your own folder.

  Alternatively, you can drag and drop any RAVE `model.ts` file manually into the `drop model.ts` box to load it.

### Buffer Size

Since the device was designed with sound material generation in mind, the "dry" incoming signal cannot be heard. The `Buffer Size` is then set by default to 4096 since response time is not the primary concern. Although, if using the external signal requires low latency, the `Buffer Size` can be set lower, to the cost of a bigger CPU load.

### Input signal

The incoming signal, onto which the timbre transfer will be applied, can be either a live signal or a sound file (or both simultaneously).  

   - To turn on the live input, press the `External` button and select the desired input channel(s).
    
   - The `Gain` knob adjusts the input level of the external signal.
    
   - The horizontal `Input Level` fader controls the overall input level of both the incoming external signal and the sound file.
 
➔ The `Input Level` appears to be a significant parameter when using RAVE models. Depending on the model, there are specific input levels where the model responds better, and others where it can completely break, producing heavy digital distortion or even silence.

---
## Patchbay (Crosspatcher)

The `crosspatcher` object on the left side of the interface offers a visual representation of the normally hidden connections between the `encoder` outlets and `decoder` inlets.

- In normal usage, latent dimensions are meant to be paired in order (0 to 0, 1 to 1, and so on). You can recall this standard setup at any point by pressing the `RESET` button. The `CLEAR` button erases all existing connections.

**One of the key features of this device is the randomization of the patchbay:**
  
  - The `SINGLE` button creates **one** random connection each time it is pressed until it has connected all dimensions.
 
  - The `ALL` button randomizes **all** connections in one click.
  
➔ When a model with more latent dimensions than the previous one is loaded, only the number of connections corresponding to the previous model will be connected. Use the `RESET` or `ALL` **button to refill the patchbay in one click.

---
## Latent Parameters and Latent Noise

The first `multislider` object displays the **Latent Dimensions** parameters.  

  - Each slider controls the value of a dimension going into the `decoder`, meaning this feature is directly linked to the patchbay's connections.  
  
  - The `RAND` and `RESET` buttons **randomize** or **reset** the entire array of sliders.

The second `multislider` object applies **Latent Noise** to the values set in the first one.  

  - Random modulation is added by adjusting the modulation amount per dimension, and the `latent noise frequency` controls the speed of this modulation.  
  
  - The `RAND` and `RESET` buttons behave the same as with the first multislider.
  
➔ When a model is loaded, the number of latent dimensions will be **automatically detected** and updated in the patchbay and both multisliders. The detected number of dimensions is displayed at the bottom right of the `Latent Noise` multislider.

---
## Presets and recording audio

- The `Presets` box allows you to easily save and recall the `input level`, patchbay connections, and the values of both multisliders.

- The `Width` parameter controls the stereo correlation between the left and right outputs, which are decorrelated by design. Setting `Width` all the way down will create a fully mono signal.

- If you wish to record audio directly inside the patch, first press the `Open` button to set the desired file format, name and directory, then press `Record` to start recording.

---
## Known Issues

- On startup, it is possible that the patchbay shows an odd number of left-hand and right-hand dimensions. Simply reload a model to fix this.
