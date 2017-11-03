<img src="resource/seanim_dark.png" alt="SEAnim" style="width: 480px;"/>

# Open-Source Binary Animation Format

## [Specification](spec.md)
SEAnim file format specification for version 1.0.2.

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
| v1.0.2 | 5dd1efb9342ceab90c77dbd689852608a17ccc74 | Revision 2 - Corrects an issue where the incorrect type could be chosen for frame_t |