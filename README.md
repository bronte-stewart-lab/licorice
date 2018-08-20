# LiCoRICE - Linux Comodular Realtime Interactive Computation Engine

LiCoRICE is a platform that performs realtime processing of data.
It is suitable for numerical processing of streaming data into and through a system.

For reliable timing the application layer must be run on a machine that is properly configured and patched with the PREEMPT\_RT kernel patch.
The instructions here should be applied to a stock install of Ubuntu server 16.04 LTS.

## Hardware Prerequisites

LiCoRICE setup:
* x86\_64 system
* at least 2 CPU cores with hyperthreading (ideally 4 or more)

## Setup

### Directory structure

LiCoRICE is designed to be a submodule of a larger repository.
This larger repository is where the configuration, code, and binaries for the models that LiCoRICE runs are stored.
An example directory structure for an experimental rig would be:

* `rig`
  * `licorice` (this is the LiCoRICE git repository)
  * `models` (directory where the YAML files specifying the various LiCoRICE models)
  * `modules` (directory where the code for the various LiCoRICE modules is kept)
  * `run` (output directory where the code and compiled binaries of the LiCoRICE model is output to)

### Installation 

1. Disable all USB and ACPI support in the BIOS of the target system

    USB and ACPI features throw CPU interrupts which can interfere with realtime performance.

    If USB support is needed for peripherals, enable only the minimum USB version necessary on as few ports as possible if such options in the BIOS exist.

2. Clone the LiCoRICE repository and place it in the directory structure as mentioend above

3. Compile realtime kernel

    From the top-level LiCoRICE repository directory, run:

    ```bash
    ./install/kernel_setup.sh
    ```

    This script will take from one to five hours to complete, depeding on the speed and processor count of the system.
    Reboot to finish installation when notified.
    Any USB keyboards will not work after this point (USB support is disabled in this realtime kernel), use a PS/2 keyboard or ssh into the system.

    Note: if USB support is necessary (e.g., system requires realtime support for a USB peripheral), install the USB enabled kernel instead via `./install/kernel_setup_usb.sh` instead. Enabling USB support will degrade system performance by a small amount, but may still fit within application tolerances. In general, a limited number of USB devices do not preclude consistently meeting 1ms ticks. Regardless, it is important to always verify timings for a given system deployment.

4. Python virtualenv setup

    From the top-level LiCoRICE directory, run:

    ```bash
    ./install/venv_setup.sh
    ```

    This script will take 15 to 30 minutes to complete.

5. Bind to the newly built virtualenv:

    ```bash
    source ~/lico_venv/bin/activate
    ```

6. Source the LiCoRICE activation script:

    ```bash
    source licorice_activate.sh
    ```

    This will make a number of shell functions available, all start with `licorice_`.

    If successful, the prompt should appear in the following manner: `[LiCoRICE] (lico_venv) <user>@<host>:~$`

## Examples

The examples folder contains instructions on how to get started with LiCoRICE.

It is recommended that first time users work through the examples to become familiar with the basics of the LiCoRICE workflow.

## Usage

In general, only one command needs to be issued to parse, compile, and run a model:

```bash
licorice_go <model_name>
```

This performs the following actions:

1. Parsing

    Once a LiCoRICE model is configured, it must be parsed to generate the code.
    This is done with the following command, and assumes that the shell is bound to the virtualenv:

    ```bash
    licorice_parse_model <model_name>
    ```

2. Compiling

    Once parsed, the code is ready to be compiled.

    ```bash
    licorice_compile_model
    ```
3. Running

    After compilation, the model can then be run:
    ```bash
    licorice_run_model
    ```

