# Models

These are the **eight exact filenames selected by the workflow**. Folder paths are relative to the ComfyUI application directory. Existing configured external model paths also work for the core loaders; keep the filenames selected in the workflow or update the corresponding loader.

LTX-2.5 and the HDR model currently require Hugging Face sign-in and acceptance of their access conditions. Open the model page first if a file download returns 401/403. Links and filenames were checked on 2026-09-27; no weights are redistributed here.

| Purpose | Exact selected file / download | Destination | Node |
|---|---|---|---|
| LTX 2.5 distilled transformer | [ltx-2.5-22b-distilled-transformer-comfy-int8-convrot.safetensors](https://huggingface.co/Lightricks/LTX-2.5/resolve/main/diffusion_models/ltx-2.5-22b-distilled-transformer-comfy-int8-convrot.safetensors) | `models/diffusion_models/` | 8 |
| Gemma 4 text encoder and projections | [gemma4-12b-with-proj-ltx-2.5-comfy-int8-convrot.safetensors](https://huggingface.co/Lightricks/LTX-2.5/resolve/main/text_encoders/gemma4-12b-with-proj-ltx-2.5-comfy-int8-convrot.safetensors) | `models/text_encoders/` | 9 |
| LTX 2.5 video VAE | [ltx-2.5-video-vae-bf16.safetensors](https://huggingface.co/Lightricks/LTX-2.5/resolve/main/vae/ltx-2.5-video-vae-bf16.safetensors) | `models/vae/` | 10 |
| LTX 2.5 audio VAE | [ltx-2.5-audio-vae-bf16.safetensors](https://huggingface.co/Lightricks/LTX-2.5/resolve/main/vae/ltx-2.5-audio-vae-bf16.safetensors) | `models/vae/` | 11 |
| Learned LTX spatial 2x upscaler | [ltx-2.5-latent-spatial-upscaler-x2-bf16-1.0.safetensors](https://huggingface.co/Lightricks/LTX-2.5/resolve/main/latent_upscale_models/ltx-2.5-latent-spatial-upscaler-x2-bf16-1.0.safetensors) | `models/latent_upscale_models/` | 141 |
| Video Depth Anything Small | [video_depth_anything_vits.pth](https://huggingface.co/depth-anything/Video-Depth-Anything-Small/resolve/main/video_depth_anything_vits.pth) | `models/videodepthanything/` | 28 |
| Union Control IC-LoRA | [ltx-2.3-22b-ic-lora-union-control-ref0.5.safetensors](https://huggingface.co/Lightricks/LTX-2.3-22b-IC-LoRA-Union-Control/resolve/main/ltx-2.3-22b-ic-lora-union-control-ref0.5.safetensors) | `models/loras/` | 33 |
| HDR IC-LoRA | [ltx-2.3-22b-ic-lora-hdr-0.9.safetensors](https://huggingface.co/Lightricks/LTX-2.3-22b-IC-LoRA-HDR/resolve/main/ltx-2.3-22b-ic-lora-hdr-0.9.safetensors) | `models/loras/` | 117 |

The installed Video Depth Anything loader specifically uses `models/videodepthanything/` under ComfyUI's model directory. When its selected file is absent, the node attempts to download that model there.

## Original model pages

- [Lightricks LTX-2.5](https://huggingface.co/Lightricks/LTX-2.5): distilled transformer, Gemma 4 encoder, video/audio VAEs, and spatial upscaler.
- [Lightricks LTX-2.3 Union Control IC-LoRA](https://huggingface.co/Lightricks/LTX-2.3-22b-IC-LoRA-Union-Control).
- [Lightricks LTX-2.3 HDR IC-LoRA](https://huggingface.co/Lightricks/LTX-2.3-22b-IC-LoRA-HDR).
- [Video Depth Anything Small](https://huggingface.co/depth-anything/Video-Depth-Anything-Small).

Consult each model page for its access conditions, license, and usage documentation. The two selected INT8-convrot files are the ComfyUI-specific transformer and text encoder exports.

## Compatibility

This is an experimental LTX-2.5 distilled workflow using LTX-2.3 Union Control and HDR adapters. The [LTX-2.5 model card](https://huggingface.co/Lightricks/LTX-2.5) describes broad compatibility with 2.3 LoRAs/IC-LoRAs with exceptions; the adapter model cards still identify LTX-2.3 as their base. This exact workflow is maintainer-tested, rather than an officially validated LTX-2.5 HDR recipe.

No separate distilled LoRA, HDR scene-embedding file, temporal upscaler, or additional pixel-upscaler model file is selected by this JSON. DLSS finishing and RTX output upscaling have their own software/runtime requirements described in [DEPENDENCIES.md](DEPENDENCIES.md).
