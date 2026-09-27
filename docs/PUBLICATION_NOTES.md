# Publication audit

Initial private publication: 2026-09-27.

Source: the maintainer's final `LTX 2.5 Depth Conditioning + Spatial Upscale + LTX HDR.json`.

Source SHA-256: `2cab26853ebde7bcebdfc7c3d62ff4ef3af379ed720d907a1d2334849fccae06`.

## Preserved behavior

The initial publication preserved all 85 nodes and 142 links. The HDR10 update adds a saver and instruction note, plus three output connections, bringing the workflow to 87 nodes and 145 links. Existing sampler settings, model selections, seeds, input connections, and enabled/bypassed states are unchanged. The original source file was not modified.

The final graph's optional finishing order is film grain → DLSS → RTX VSR, after HDR tonemapping. This supersedes earlier versions that placed RTX/DLSS before HDR.

## Metadata cleanup

- Removed cached VideoHelperSuite previews, including machine-local output paths.
- Removed the cached HDR preview frame list.
- Removed obsolete internal audit history from `extra` (`selfdepth`, `comparison_repair`, `seam_repair`, `hdr_integration`). Those records referenced previous graph revisions.
- Corrected node 151's `aux_id` to `exportAnything/ComfyUI-DLSS5-NR-Temporal`.
- Corrected node 150's title to describe its actual position after HDR.

The reference image filename remains selected, but the image is not distributed. Users must upload their own image.

## Dependency findings

Core LTX implementation files match the installed official ComfyUI v0.33.1 baseline. LTXVideo and Video Depth Anything have no local code changes needed by this workflow. KJNodes, VideoHelperSuite, comfy-mtb, and Easy-Use source/configuration were compared with their corresponding official registry release archives. RES4LYF and MemoryCleaner have no relevant tracked source changes.

The DLSS temporal-motion implementation is already published at `81171d18bc2a359d75cace2eadf13efc757d9b2d`. The author's newer local edits in `nodes.py`, `README.md`, and `tests/test_temporal.py` add streaming sessions and resource cleanup. The local RTX package also has session-reuse/cache-policy additions in `__init__.py`, supporting documentation, and tests. These additional interfaces are for other integrations and are not new requirements of this whole-batch workflow.

Those optional implementations are not byte-for-byte identical to the author's current installation. Their local session changes are not vendored or committed here. Published versions provide the node interfaces used by this graph; an independent clean-install render has not been performed as part of publication.

The previously unused local HDR10 preset is now included in `video_formats/` and connected to the raw LogC3 decoder output in parallel with the SDR preview. Install it as described in [HDR10.md](HDR10.md). It uses VideoHelperSuite's 16-bit RGB input pipe, inverse LogC3, linear Rec.709 to PQ/BT.2020 conversion, and HEVC Main10 encoding. SDR finishing remains on its original branch.

## Validation scope

- Compared the published copy's execution-relevant data against the original workflow.
- Checked node/link identities, link endpoints, and reciprocal input/output references.
- Confirmed model filenames against the local loaders and original model listings.
- Verified custom-node repository links and the published DLSS fork commit.
- Preserved the maintainer's working generation settings; no new render was launched for this documentation/publication task.

The HDR10 update is validated separately with synthetic LogC3 inputs, encoded-pixel checks, container/bitstream metadata inspection, and audio muxing. See [HDR10 verification](HDR10.md#verification). This checks the delivery path, not a new full diffusion render or a subjective HDR display grade.

Model weights and third-party software remain subject to their original source licenses and access conditions. This repository distributes the workflow, encoder preset, and documentation.
