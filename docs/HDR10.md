# True HDR10 output

The HDR10 saver branches directly from **node 126, the raw LogC3 HDR VAE decoder**. It receives the original depth-pass audio from node 43 and the shared FPS from node 6. The SDR tonemapped preview remains available on its separate branch.

The conversion is:

```text
Raw normalized LogC3 RGB
  -> 16-bit RGB pipe into FFmpeg
  -> inverse LogC3, linear Rec.709
  -> exposure and 1000-nit ceiling
  -> linear Rec.709 to BT.2020 gamut conversion
  -> PQ / SMPTE ST 2084
  -> limited-range 10-bit HEVC Main10 in MP4
  -> original audio muxed as AAC
```

This transforms the pixel values into HDR; it does not simply attach HDR tags to an SDR video. The source color interpretation follows [Lightricks' LogC3 HDR implementation](https://github.com/Lightricks/LTX-2/blob/main/packages/ltx-core/src/ltx_core/hdr.py).

## Installation

1. Install [VideoHelperSuite](https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite) and an FFmpeg build that includes `libx265`, `zscale`, and `geq`. See [FFmpeg downloads](https://ffmpeg.org/download.html).
2. Copy the bundled [ltx-logc3-hdr10-1000nit-hevc-mp4.json](../video_formats/ltx-logc3-hdr10-1000nit-hevc-mp4.json) into:

   ```text
   ComfyUI/custom_nodes/comfyui-videohelpersuite/video_formats/
   ```

3. Refresh the ComfyUI page; restart ComfyUI if the format is not listed. Check that the new saver selects `video/ltx-logc3-hdr10-1000nit-hevc-mp4`.
4. Enable the LTX upscale, HDR generation, and **15 HDR10 OUTPUT** nodes. The saved file starts these stages bypassed, as in the maintainer's staged workflow. The HDR10 saver must not run with the HDR processing bypassed.
5. Queue the workflow. The HDR file is saved under `output/selfdepth/LTX25/HDR10/03_HDR10_1000nit*`. With audio connected, the muxed file ends in `-audio.mp4`.

No new custom node or extra model is required. The preset is required in addition to the workflow JSON. Reinstall it if a VideoHelperSuite update removes local custom formats. Existing installations that already contain this exact preset do not need a second copy.

To inspect FFmpeg's capabilities:

```text
ffmpeg -hide_banner -encoders
ffmpeg -hide_banner -filters
```

Look for `libx265` among encoders and `zscale`/`geq` among filters. VideoHelperSuite can select a different FFmpeg binary from the one on your shell's PATH; check its selected binary if a filter is reported missing. The HDR10 preset uses CPU encoding rather than NVENC.

## Controls and output target

| Setting | Default | Meaning |
|---|---|---|
| `reference_white_nits` | 203 | Maps a decoded linear RGB value of 1.0 to 203 nits. |
| `hdr_exposure` | 0 | Exposure adjustment in stops, applied to the HDR branch before PQ conversion. |
| `crf` | 18 | x265 quality setting; lower values increase quality and file size. |
| Highlight ceiling | 1000 nits | Fixed hard clip per RGB channel. Values above this ceiling are clipped. |
| Output signal | PQ / BT.2020 | `smpte2084` transfer, `bt2020` primaries, `bt2020nc` matrix, limited range. |
| Codec / pixel format | HEVC Main10 / `yuv420p10le` | 10-bit 4:2:0 in MP4, `hvc1` tag. |
| Mastering metadata | BT.2020 / D65, 1000 / 0.0001 nits | Declares the selected delivery target. |
| MaxCLL / MaxFALL | Unspecified (`0,0`) | Content-light measurements have not been calculated. |

The default is a practical HDR delivery transform, not a professionally measured or display-graded master. See [x265's HDR metadata options](https://x265.readthedocs.io/en/master/cli.html#cmdoption-master-display) for the distinction between mastering-display metadata and measured content light levels.

`HDRPreviewKJ` exposure and saturation only affect the SDR preview. Its film grain, DLSS, and RTX finishing also remain on that SDR branch. These operations are not applied to raw LogC3 data or to the HDR10 file. The HDR10 output therefore remains at the LTX upscale dimensions, 2688x1536 with the default settings.

Use an HDR-capable player/display to judge the HDR10 file. Browser or ComfyUI previews may not present PQ video correctly. The SDR branch is available for ordinary displays and comparisons.

## Verification

The bundled preset was tested using synthetic LogC3 grayscale patches and a gradient. The test used VideoHelperSuite's own format expansion and float-to-16-bit conversion functions, the preset's FFmpeg conversion/encoder, and its copy-video/AAC-audio mux. No diffusion render was needed for this delivery test.

The encoded file was verified as HEVC Main10, `yuv420p10le`, limited range, BT.2020 primaries/matrix, PQ transfer, and 1000-nit mastering metadata. All eight test frames and audio decoded successfully. Independent decoding used PyAV and FFmpeg 7.1.

| Neutral patch luminance | Expected 10-bit limited-range Y | Decoded median Y |
|---|---:|---:|
| Black | 64 | 64 |
| 36.54 nits | 424 | 424 |
| Reference white: 203 nits | 573 | 573 |
| Peak: 1000 nits | 723 | 723 |

The sampled decoded gradient contained 334 distinct luma codes. Together with the 16-bit input path and brightness checks, this verifies actual HDR signal conversion rather than metadata alone. Compression and dithering can produce small local variations.

Inspect your saved file with:

```text
ffprobe -v error -select_streams v:0 -show_streams -show_frames -read_intervals "%+#1" -of json your_HDR10-audio.mp4
```

Check `profile`, `pix_fmt`, `color_transfer`, `color_primaries`, `color_space`, and the frame's mastering-display side data. These checks establish encoding and metadata; they do not validate subjective image quality or the experimental LTX-2.5/2.3-HDR-LoRA model combination.
