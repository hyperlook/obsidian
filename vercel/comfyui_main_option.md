---
title: comfyui main.py 参数
tags:
  - comfyui
  - 图像生成
  - AI
created: 2025-03-22 17:36
---
usage: main.py [-h] [--listen [IP]] [--port PORT] [--tls-keyfile TLS_KEYFILE]
               [--tls-certfile TLS_CERTFILE] [--enable-cors-header [ORIGIN]]
               [--max-upload-size MAX_UPLOAD_SIZE]
               [--base-directory BASE_DIRECTORY]
               [--extra-model-paths-config PATH [PATH ...]]
               [--output-directory OUTPUT_DIRECTORY]
               [--temp-directory TEMP_DIRECTORY]
               [--input-directory INPUT_DIRECTORY] [--auto-launch]
               [--disable-auto-launch] [--cuda-device DEVICE_ID]
               [--cuda-malloc | --disable-cuda-malloc]
               [--force-fp32 | --force-fp16]
               [--fp32-unet | --fp64-unet | --bf16-unet | --fp16-unet | --fp8_e4m3fn-unet | --fp8_e5m2-unet]
               [--fp16-vae | --fp32-vae | --bf16-vae] [--cpu-vae]
               [--fp8_e4m3fn-text-enc | --fp8_e5m2-text-enc | --fp16-text-enc | --fp32-text-enc]
               [--force-channels-last] [--directml [DIRECTML_DEVICE]]
               [--oneapi-device-selector SELECTOR_STRING]
               [--disable-ipex-optimize]
               [--preview-method [none,auto,latent2rgb,taesd]]
               [--preview-size PREVIEW_SIZE]
               [--cache-classic | --cache-lru CACHE_LRU]
               [--use-split-cross-attention | --use-quad-cross-attention | --use-pytorch-cross-attention | --use-sage-attention | --use-flash-attention]
               [--disable-xformers]
               [--force-upcast-attention | --dont-upcast-attention]
               [--gpu-only | --highvram | --normalvram | --lowvram | --novram | --cpu]
               [--reserve-vram RESERVE_VRAM]
               [--default-hashing-function {md5,sha1,sha256,sha512}]
               [--disable-smart-memory] [--deterministic] [--fast [FAST ...]]
               [--dont-print-server] [--quick-test-for-ci]
               [--windows-standalone-build] [--disable-metadata]
               [--disable-all-custom-nodes] [--multi-user]
               [--verbose [{DEBUG,INFO,WARNING,ERROR,CRITICAL}]]
               [--log-stdout] [--front-end-version FRONT_END_VERSION]
               [--front-end-root FRONT_END_ROOT]
               [--user-directory USER_DIRECTORY]
               [--enable-compress-response-body]

options:
  -h, --help            show this help message and exit
  --listen [IP]         Specify the IP address to listen on (default:
                        127.0.0.1). You can give a list of ip addresses by
                        separating them with a comma like: 127.2.2.2,127.3.3.3
                        If --listen is provided without an argument, it
                        defaults to 0.0.0.0,:: (listens on all ipv4 and ipv6)
  --port PORT           Set the listen port.
  --tls-keyfile TLS_KEYFILE
                        Path to TLS (SSL) key file. Enables TLS, makes app
                        accessible at https://... requires --tls-certfile to
                        function
  --tls-certfile TLS_CERTFILE
                        Path to TLS (SSL) certificate file. Enables TLS, makes
                        app accessible at https://... requires --tls-keyfile
                        to function
  --enable-cors-header [ORIGIN]
                        Enable CORS (Cross-Origin Resource Sharing) with
                        optional origin or allow all with default '*'.
  --max-upload-size MAX_UPLOAD_SIZE
                        Set the maximum upload size in MB.
  --base-directory BASE_DIRECTORY
                        Set the ComfyUI base directory for models,
                        custom_nodes, input, output, temp, and user
                        directories.
  --extra-model-paths-config PATH [PATH ...]
                        Load one or more extra_model_paths.yaml files.
  --output-directory OUTPUT_DIRECTORY
                        Set the ComfyUI output directory. Overrides --base-
                        directory.
  --temp-directory TEMP_DIRECTORY
                        Set the ComfyUI temp directory (default is in the
                        ComfyUI directory). Overrides --base-directory.
  --input-directory INPUT_DIRECTORY
                        Set the ComfyUI input directory. Overrides --base-
                        directory.
  --auto-launch         Automatically launch ComfyUI in the default browser.
  --disable-auto-launch
                        Disable auto launching the browser.
  --cuda-device DEVICE_ID
                        Set the id of the cuda device this instance will use.
  --cuda-malloc         Enable cudaMallocAsync (enabled by default for torch
                        2.0 and up).
  --disable-cuda-malloc
                        Disable cudaMallocAsync.
  --force-fp32          Force fp32 (If this makes your GPU work better please
                        report it).
  --force-fp16          Force fp16.
  --fp32-unet           Run the diffusion model in fp32.
  --fp64-unet           Run the diffusion model in fp64.
  --bf16-unet           Run the diffusion model in bf16.
  --fp16-unet           Run the diffusion model in fp16
  --fp8_e4m3fn-unet     Store unet weights in fp8_e4m3fn.
  --fp8_e5m2-unet       Store unet weights in fp8_e5m2.
  --fp16-vae            Run the VAE in fp16, might cause black images.
  --fp32-vae            Run the VAE in full precision fp32.
  --bf16-vae            Run the VAE in bf16.
  --cpu-vae             Run the VAE on the CPU.
  --fp8_e4m3fn-text-enc
                        Store text encoder weights in fp8 (e4m3fn variant).
  --fp8_e5m2-text-enc   Store text encoder weights in fp8 (e5m2 variant).
  --fp16-text-enc       Store text encoder weights in fp16.
  --fp32-text-enc       Store text encoder weights in fp32.
  --force-channels-last
                        Force channels last format when inferencing the
                        models.
  --directml [DIRECTML_DEVICE]
                        Use torch-directml.
  --oneapi-device-selector SELECTOR_STRING
                        Sets the oneAPI device(s) this instance will use.
  --disable-ipex-optimize
                        Disables ipex.optimize default when loading models
                        with Intel's Extension for Pytorch.
  --preview-method [none,auto,latent2rgb,taesd]
                        Default preview method for sampler nodes.
  --preview-size PREVIEW_SIZE
                        Sets the maximum preview size for sampler nodes.
  --cache-classic       Use the old style (aggressive) caching.
  --cache-lru CACHE_LRU
                        Use LRU caching with a maximum of N node results
                        cached. May use more RAM/VRAM.
  --use-split-cross-attention
                        Use the split cross attention optimization. Ignored
                        when xformers is used.
  --use-quad-cross-attention
                        Use the sub-quadratic cross attention optimization .
                        Ignored when xformers is used.
  --use-pytorch-cross-attention
                        Use the new pytorch 2.0 cross attention function.
  --use-sage-attention  Use sage attention.
  --use-flash-attention
                        Use FlashAttention.
  --disable-xformers    Disable xformers.
  --force-upcast-attention
                        Force enable attention upcasting, please report if it
                        fixes black images.
  --dont-upcast-attention
                        Disable all upcasting of attention. Should be
                        unnecessary except for debugging.
  --gpu-only            Store and run everything (text encoders/CLIP models,
                        etc... on the GPU).
  --highvram            By default models will be unloaded to CPU memory after
                        being used. This option keeps them in GPU memory.
  --normalvram          Used to force normal vram use if lowvram gets
                        automatically enabled.
  --lowvram             Split the unet in parts to use less vram.
  --novram              When lowvram isn't enough.
  --cpu                 To use the CPU for everything (slow).
  --reserve-vram RESERVE_VRAM
                        Set the amount of vram in GB you want to reserve for
                        use by your OS/other software. By default some amount
                        is reserved depending on your OS.
  --default-hashing-function {md5,sha1,sha256,sha512}
                        Allows you to choose the hash function to use for
                        duplicate filename / contents comparison. Default is
                        sha256.
  --disable-smart-memory
                        Force ComfyUI to agressively offload to regular ram
                        instead of keeping models in vram when it can.
  --deterministic       Make pytorch use slower deterministic algorithms when
                        it can. Note that this might not make images
                        deterministic in all cases.
  --fast [FAST ...]     Enable some untested and potentially quality
                        deteriorating optimizations. --fast with no arguments
                        enables everything. You can pass a list specific
                        optimizations if you only want to enable specific
                        ones. Current valid optimizations: fp16_accumulation
                        fp8_matrix_mult
  --dont-print-server   Don't print server output.
  --quick-test-for-ci   Quick test for CI.
  --windows-standalone-build
                        Windows standalone build: Enable convenient things
                        that most people using the standalone windows build
                        will probably enjoy (like auto opening the page on
                        startup).
  --disable-metadata    Disable saving prompt metadata in files.
  --disable-all-custom-nodes
                        Disable loading all custom nodes.
  --multi-user          Enables per-user storage.
  --verbose [{DEBUG,INFO,WARNING,ERROR,CRITICAL}]
                        Set the logging level
  --log-stdout          Send normal process output to stdout instead of stderr
                        (default).
  --front-end-version FRONT_END_VERSION
                        Specifies the version of the frontend to be used. This
                        command needs internet connectivity to query and
                        download available frontend implementations from
                        GitHub releases. The version string should be in the
                        format of: [repoOwner]/[repoName]@[version] where
                        version is one of: "latest" or a valid version number
                        (e.g. "1.0.0")
  --front-end-root FRONT_END_ROOT
                        The local filesystem path to the directory where the
                        frontend is located. Overrides --front-end-version.
  --user-directory USER_DIRECTORY
                        Set the ComfyUI user directory with an absolute path.
                        Overrides --base-directory.
  --enable-compress-response-body
                        Enable compressing response body.
