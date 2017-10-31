<img src="resource/seanim_dark.png" alt="SEAnim" style="width: 480px;"/>

# Open-Source Binary Animation Format

## [Specification](spec.md)
SEAnim file format specification for version 1.0.1.

## Plugins
SEAnim plugins for various 3D software suites.
+ [io_anim_seanim](https://github.com/SE2Dev/io_anim_seanim) (Blender)
+ [SETools](https://github.com/dtzxporter/SETools) (Maya)

## Libraries
Libraries that handle reading / writing SEAnim files.
+ [seanim.py](https://github.com/SE2Dev/io_anim_seanim/blob/master/seanim.py) (Python) - Part of the *io_anim_seanim* plugin above
+ [SELibDotNet](https://github.com/dtzxporter/SELibDotNet) (.NET)

## Changes
| Version | Commit | Remarks |
|:--------|:-------|:--------|
| v1.0.0 | b8f36bb7d9ab450884f640d25cef1f77253ccffc | Initial Release |
| v1.0.1 | 3c93edaabc6a34f59e4dbb92a493ddd2265a9668 | Revision 1 - Corrects various spec issues with frame_t and bone_t; Clarifies ambiguous definitions |
| v1.1.0 | 741bdab3d07b464d2a8aaac3d2bb74d546e44cf5 | Revision 2 - Fixes frame_t so that animations of 0xFF and 0xFFFF frames may load properly |
