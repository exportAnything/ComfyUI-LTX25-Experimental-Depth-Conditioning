# Publication audit

Initial private publication: 2026-09-27.

Initial source: the maintainer's `LTX 2.5 Depth Conditioning + Spatial Upscale + LTX HDR.json`.

Initial source SHA-256: `2cab26853ebde7bcebdfc7c3d62ff4ef3af379ed720d907a1d2334849fccae06`.

Current source (2026-09-27): `LTX_2.5_Distilled_Experimental_Depth_Conditioning HDR10_Update (1).json`.

Current source SHA-256: `c486cca070c97ef9d653035bb846d38028bfe46dc41d0fe2af154a2af9749c23`.

## Preserved behavior

The initial publication preserved all 85 nodes and 142 links. The first HDR10 update added a saver and instruction note, plus three output connections. The current maintainer revision still has 87 nodes and 145 links, with a dedicated RTX upscale between raw LogC3 decoder 126 and HDR10 saver 191.

Both HDR RTX (194) and SDR RTX (186) target 3840x2192 at LOW quality. The SDR finishing order is now film grain → RTX VSR → DLSS, after HDR tonemapping. The HDR branch does not pass through SDR tonemapping. The latest source's processing settings, model selections, seeds, connections, layout, and enabled/bypassed states are preserved. The source file was not modified.

## Metadata cleanup

Initial publication cleanup:

- Removed cached VideoHelperSuite previews, including machine-local output paths.
- Removed the cached HDR preview frame list.
- Removed obsolete internal audit history from `extra` (`selfdepth`, `comparison_repair`, `seam_repair`, `hdr_integration`). Those records referenced previous graph revisions.
- Corrected node 151's `aux_id` to `exportAnything/ComfyUI-DLSS5-NR-Temporal`.
- Corrected node 150's title to describe its actual position after HDR.

Current revision cleanup removes cached VideoHelperSuite/HDR previews, updates the two RTX titles to show their actual 3840x2192 targets, and updates instruction note 183 for the new HDR upscale connection. No processing controls were changed.

The reference image filename remains selected, but the image is not distributed. Users must upload their own image.

## Dependency findings

Core LTX implementation files match the installed official ComfyUI v0.33.1 baseline. LTXVideo and Video Depth Anything have no local code changes needed by this workflow. KJNodes, VideoHelperSuite, comfy-mtb, and Easy-Use source/configuration were compared with their corresponding official registry release archives. RES4LYF and MemoryCleaner have no relevant tracked source changes.

The DLSS temporal-motion implementation is already published at `81171d18bc2a359d75cace2eadf13efc757d9b2d`. The author's newer local edits in `nodes.py`, `README.md`, and `tests/test_temporal.py` add streaming sessions and resource cleanup. The local RTX package also has session-reuse/cache-policy additions in `__init__.py`, supporting documentation, and tests. These additional interfaces are for other integrations and are not new requirements of this whole-batch workflow.

Those optional implementations are not byte-for-byte identical to the author's current installation. Their local session changes are not vendored or committed here. Published versions provide the node interfaces used by this graph; an independent clean-install render has not been performed as part of publication.

The HDR10 preset is included in `video_formats/` and receives raw LogC3 through the HDR branch's RTX VSR node, separately from the SDR preview. Install it as described in [HDR10.md](HDR10.md). It uses VideoHelperSuite's 16-bit RGB input pipe, inverse LogC3, linear Rec.709 to PQ/BT.2020 conversion, and HEVC Main10 encoding. The preset is unchanged in this revision.

## Validation scope

- Compared the published copy's execution-relevant data and layout against the latest source workflow.
- Checked node/link identities, link endpoints, and reciprocal input/output references.
- Confirmed model filenames against the local loaders and original model listings.
- Verified custom-node repository links and the published DLSS fork commit.
- Preserved the maintainer's working generation settings; no new render was launched for this documentation/publication task.

The HDR10 preset was validated separately with synthetic LogC3 inputs, encoded-pixel checks, container/bitstream metadata inspection, and audio muxing. See [HDR10 verification](HDR10.md#verification). The current RTX-to-HDR10 branch was checked structurally, without a new RTX render. The wrapper uses float32 input with no explicit 8-bit conversion; this does not establish the neural upscaler's LogC3 color accuracy or visual quality.

Model weights and third-party software remain subject to their original source licenses and access conditions. This repository distributes the workflow, encoder preset, and documentation.
