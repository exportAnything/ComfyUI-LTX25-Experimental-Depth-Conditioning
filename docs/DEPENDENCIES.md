# ComfyUI and custom nodes

The audited installation uses [ComfyUI 0.33.1](https://github.com/Comfy-Org/ComfyUI/releases/tag/v0.33.1) with frontend 1.48.7. The LTX model loaders, Gemma support, samplers, latent upscaler, and tiled VAE decoder are core nodes. There is no additional custom LTX upscaling implementation to install from this repository.

Install the packages below using your ComfyUI node manager or each project's own installation instructions, including its Python requirements, then restart ComfyUI. The saved graph includes optional bypassed nodes; installing their packages avoids missing-node placeholders even when those stages are disabled.

## Generation, upscale, and HDR dependencies

| Package | Audited version / revision | Nodes used |
|---|---|---|
| [ComfyUI-LTXVideo](https://github.com/Lightricks/ComfyUI-LTXVideo) | [`ac4d998`](https://github.com/Lightricks/ComfyUI-LTXVideo/commit/ac4d99839020b983e956a8ab67ec38aec1b6e65a) | `LTXICLoRALoaderModelOnly`, `LTXAddVideoICLoRAGuide` |
| [ComfyUI-Video-Depth-Anything](https://github.com/yuvraj108c/ComfyUI-Video-Depth-Anything) | [`a0db08e`](https://github.com/yuvraj108c/ComfyUI-Video-Depth-Anything/commit/a0db08e63d1ea571601c45cde4aaee0acdd0544d) | `LoadVideoDepthAnythingModel`, `VideoDepthAnythingProcess`, `VideoDepthAnythingOutput` |
| [ComfyUI-KJNodes](https://github.com/kijai/ComfyUI-KJNodes) | 1.4.8 | `HDRPreviewKJ`, `GetImageSizeAndCount` |
| [ComfyUI-VideoHelperSuite](https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite) | 1.7.9 | `VHS_VideoCombine`; stock AV1 preview format and the bundled HDR10 format |
| [comfy-mtb](https://github.com/melMass/comfy_mtb) | 0.5.4 | `Image Resize Factor (mtb)` |

## Optional finishing and memory helpers

| Package | Audited version / revision | Nodes used |
|---|---|---|
| [ComfyUI-DLSS5-NR-Temporal](https://github.com/exportAnything/ComfyUI-DLSS5-NR-Temporal) | [`81171d1`](https://github.com/exportAnything/ComfyUI-DLSS5-NR-Temporal/commit/81171d18bc2a359d75cace2eadf13efc757d9b2d) | `DLSS5NeuralRendering` |
| [NVIDIA RTX Nodes for ComfyUI](https://github.com/Comfy-Org/Nvidia_RTX_Nodes_ComfyUI) | 0.1.3 | `RTXVideoSuperResolution` |
| [RES4LYF](https://github.com/ClownsharkBatwing/RES4LYF) | [`e716cd1`](https://github.com/ClownsharkBatwing/RES4LYF/commit/e716cd1cb2c5cff90131bf4914b75b75a0489d48) | `Film Grain` |
| [ComfyUI-Easy-Use](https://github.com/yolain/ComfyUI-Easy-Use) | 1.3.6 | `easy cleanGpuUsed` |
| [ComfyUI-MemoryCleaner](https://github.com/eddyhhlure1Eddy/ComfyUI-MemoryCleaner) | [`6ff10c1`](https://github.com/eddyhhlure1Eddy/ComfyUI-MemoryCleaner/commit/6ff10c1ec7aa25cce2e10311f004df60426cb1a7) | `MemoryCleaner` |

These nodes are bypassed in the saved file. Optional film grain, DLSS, and RTX VSR run after HDR tonemapping in that order.

### Use the temporal DLSS fork

Use **exportAnything/ComfyUI-DLSS5-NR-Temporal**, rather than assuming the original upstream repository supplies the tested temporal behavior. The node keeps the same class name as [the original project](https://github.com/lisitskyaa/ComfyUI-DLSS5-NR); do not enable both packages together. The publication copy's dependency annotation points to the temporal fork.

Follow the fork's [README](https://github.com/exportAnything/ComfyUI-DLSS5-NR-Temporal#readme) and [release instructions](https://github.com/exportAnything/ComfyUI-DLSS5-NR-Temporal/releases). Its temporal-motion path requires the matching native bridge, Windows/NVIDIA support, and a compatible NVIDIA Neural Rendering runtime. A source-code archive alone is not the prebuilt installation. Runtime binaries are not included here.

The already-published commit supports the workflow's `temporal sequence`, `motion_guidance=true`, and `scene_cut_threshold=0.24` inputs. Additional local streaming/session changes found in the author's installation are not a prerequisite for this graph. See [publication notes](PUBLICATION_NOTES.md).

### Encoding and RTX

The SDR output nodes select VideoHelperSuite's stock NVENC AV1 MP4 format. Use an FFmpeg build and NVIDIA device that support that encoder, or select a supported SDR format. See [VideoHelperSuite's installation documentation](https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite#readme).

**HDR10 additionally requires the bundled format file.** Copy [ltx-logc3-hdr10-1000nit-hevc-mp4.json](../video_formats/ltx-logc3-hdr10-1000nit-hevc-mp4.json) into `ComfyUI/custom_nodes/comfyui-videohelpersuite/video_formats/` and refresh/restart ComfyUI. FFmpeg must support `libx265` and `zscale`. The workflow JSON alone does not install this preset. See [HDR10 setup](HDR10.md).

Optional RTX VSR uses the NVIDIA VFX dependency described by [NVIDIA RTX Nodes](https://github.com/Comfy-Org/Nvidia_RTX_Nodes_ComfyUI). It is separate from the learned LTX latent 2x stage.

## Not required

The former local `ComfyUI-LTX-Compat` guider nodes are not referenced and do not need installation. The HDR10 export preset, previously unused, is now a bundled dependency. Model weights are listed separately in [MODELS.md](MODELS.md).
