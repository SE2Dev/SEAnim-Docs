<img src="resource/seanim_dark.png" alt="SEAnim" style="width: 480px;"/>

# Open-Source Binary Animation Format

This document describes the specification for version 1 of the SEAnim file format.

## Basic Data Types
The following types are used through this specification file. 

```c++
//
// Fundamental data types
//
typedef unsigned char	uint8_t;	// 1 byte unsigned integer
typedef unsigned short	uint16_t;	// 2 byte unsigned integer
typedef unsigned long	uint32_t;	// 4 byte unsigned integer

//
// string_t is used in this document to  describe a null terminated array of characters (a string)
//
typedef char string_t[];

#if dataPropertyFlags & SEANIM_PRECISION_HIGH
	typedef double vec3[3];
	typedef double quat_t[4]; // X Y Z W (Normalized)
#else
	typedef float vec3[3];
	typedef float quat_t[4]; // X Y Z W (Normalized)
#endif

//
// The frame_t data type is used to describe frame indices and offsets, its size is automatically determined by the number of frames in the animation
//
#if frameCount - 1 <= 0xFF
	typedef uint8_t frame_t;
#elif frameCount - 1 <= 0xFFFF
	typedef uint16_t frame_t;
#else //elif frameCount - 1 <= 0xFFFFFFFF
	typedef uint32_t frame_t;
#endif

//
// bone_t data type is used to describe bone indicies, its size is automatically determined by the number of bones in the animation
//
#if boneCount <= 0xFF
	typedef uint8_t bone_t;
#elif boneCount <= 0xFFFF
	typedef uint16_t bone_t;
#else //elif boneCount <= 0xFFFFFFFF
	typedef uint32_t bone_t;
#endif

// Position values use centimeters (cm)
```
---
## SEAnim File Structure
The general structure of a *.seanim file conists of a 6 byte magic value containing the characters 'SEAnim' followed by a 16bit version identifier, the file [header](#seanim-header), and then the data blocks.
The data blocks must follow the order defined below; Although each of these data blocks is optional, there must be *at least* 1 data block (excluding the custom data block) present within a given file. (See [here](#seanim-data-flags) for more information on how to describe the presence of each of these data blocks).
```c++
struct SEAnim_File
{
	char magic[6];		// 'SEAnim'
	uint16_t version;	// The file version - the current version is 0x1
	SEAnim_Header header;
	
	if (header.dataPresenceFlags & SEANIM_PRESENCE_BONE)
	{
		string_t bone[header.boneCount];
		SEAnim_BoneModifier[header.boneAnimModifierCount];
		SEAnim_BoneData[header.boneCount];
	}

	if (header.dataPresenceFlags & SEANIM_PRESENCE_NOTE)
	{
		SEAnim_Note notes[header.noteCount];
	}

	if (header.dataPresenceFlags & SEANIM_PRESENCE_CUSTOM)
	{
		uint32_t size;
		uint8_t data[size];
	}
}
```

## SEAnim_Header
The following defines the structure for the header structure of a SEAnim file. Any reserved fields should be set to 0.
```c++
struct SEAnim_Header
{
	// Contains the size of the header block (excluding headerSize itself) in bytes, any extra data is ignored
	uint16_t headerSize; // Currently 0x1C

	// The type of animation data that is stored, matches SEANIM_TYPE enum
	uint8_t animType;

	// Bitwise flags that define the properties for the animation itself, matches SEANIM_FLAGS enum
	uint8_t animFlags;

	// Bitwise flags that define which data blocks are present, and properties for those data blocks, matches SEANIM_PRESENCE_FLAGS enum
	uint8_t dataPresenceFlags;

	// Bitwise flags containing property information pertaining regarding the data in the file, matches SEANIM_PROPERTY_FLAGS enum
	uint8_t dataPropertyFlags;

	// RESERVED - Should be 0
	uint8_t reserved1[2];
	
	// The framerate at which this animation plays at
	float framerate;

	// frameCount describes the length of the animation (in frames)
	// It is used to determine the size of frame_t
	// This should be equal to the largest frame number in the anim (Including keys & notes) plus 1
	// Ex: An anim with keys on frame 0, 5 and 8 - frameCount would be 9
	//     An anim with keys on frame 4, 7, and 14 - frameCount would be 15
	uint32_t frameCount;

	// Is 0 if ( dataPresenceFlags & SEANIM_PRESENCE_BONE ) is false
	uint32_t boneCount;
	
	// The number of animType modifier bones
	uint8_t boneAnimModifierCount;

	// RESERVED - Should be 0
	uint8_t reserved2[3];

	// Is 0 if ( dataPresenceFlags & SEANIM_PRESENCE_NOTE ) is false
	uint32_t noteCount;
}
```
## SEANIM_TYPE
In a SEANIM_TYPE_DELTA animation, the first bone will contain the delta information for the animation.
```c++
enum SEANIM_TYPE
{
	SEANIM_TYPE_ABSOLUTE,
	SEANIM_TYPE_ADDITIVE,
	SEANIM_TYPE_RELATIVE,
	SEANIM_TYPE_DELTA,
}
```
---

## SEANIM_PRESENCE_FLAGS
SEANIM_PRESENCE_FLAGS describes the bitfields used by dataPresenceFlags in the [header](#seanim-header). It only contains information pertaining to the presence of types of data included in the file.
```c++
enum SEANIM_PRESENCE_FLAGS
{
	// These describe what type of keyframe data is present for the bones
	SEANIM_BONE_LOC		= 1 << 0,
	SEANIM_BONE_ROT		= 1 << 1,
	SEANIM_BONE_SCALE	= 1 << 2,

	// If any of the above flags are set, then bone keyframe data is present, thus this comparing against this mask will return true
	SEANIM_PRESENCE_BONE	= SEANIM_BONE_LOC | SEANIM_BONE_ROT | SEANIM_BONE_SCALE,
	
	//RESERVED_0		= 1 << 3, // ALWAYS FALSE
	//RESERVED_1		= 1 << 4, // ALWAYS FALSE
	//RESERVED_2		= 1 << 5, // ALWAYS FALSE
	
	
	SEANIM_PRESENCE_NOTE	= 1 << 6, // The file contains notetrack data
	SEANIM_PRESENCE_CUSTOM	= 1 << 7, // The file contains a custom data block
}
```

## SEANIM_PROPERTY_FLAGS
SEANIM_PROPERTY_FLAGS describes the bitfields used by dataPropertyFlags in the [header](#seanim-header). It only contains information pertaining to the PROPERTY of types of data included in the file.
```c++
enum SEANIM_PROPERTY_FLAGS
{
	SEANIM_PRECISION_HIGH =	1 << 0, // Use double precision floating point vectors instead of single precision
	//RESERVED_1		= 1 << 1, // ALWAYS FALSE
	//RESERVED_2		= 1 << 2, // ALWAYS FALSE
	//RESERVED_3		= 1 << 3, // ALWAYS FALSE
	//RESERVED_4		= 1 << 4, // ALWAYS FALSE
	//RESERVED_5		= 1 << 5, // ALWAYS FALSE
	//RESERVED_6		= 1 << 6, // ALWAYS FALSE
	//RESERVED_7		= 1 << 7, // ALWAYS FALSE
}
```

SEANIM_BONE_LOC, ROT, and SCALE all describe the type of data present in the SEANIM_PRESENCE_BONE block. All three of these flags are optional, and if all three are false, the bone block must be absent from the file. In this situation, the animation file would likely only contain custom data or notes.
Custom data blocks can used to embed metadata or other custom information within the animation file. If a custom block is present, the SEANIM_PRESENCE_CUSTOM flag must be true.

---
## SEANIM_FLAGS
SEANIM_FLAGS describes the bitfields used by animFlags in the [header](#seanim-header). It used only to define property flags that apply to the entire animation.
```c++
enum SEANIM_FLAGS
{
	SEANIM_LOOPED		= 1 << 0, // The animation is a looping animation
	//RESERVED_0		= 1 << 1, // ALWAYS FALSE
	//RESERVED_1		= 1 << 2, // ALWAYS FALSE
	//RESERVED_2		= 1 << 3, // ALWAYS FALSE
	//RESERVED_3		= 1 << 4, // ALWAYS FALSE
	//RESERVED_4		= 1 << 5, // ALWAYS FALSE
	//RESERVED_5		= 1 << 6, // ALWAYS FALSE
	//RESERVED_6		= 1 << 7, // ALWAYS FALSE
}
```

## SEAnim_BoneAnimModifier
This class contains information regarding bones that override the anim type for themselves as well as all of their children (recursively). If a child of a modifier bone is also a modifier bone, that child and all of its children (recursively) use that child's override value.
```c++
struct SEAnim_BoneAnimModifier
{
	bone_t index; // Index of the bone
	uint8_t animTypeOverride; // AnimType to use for that bone, and its children recursively
}
```

## SEAnim_BoneData
```c++
struct SEAnim_BoneData
{
	/*
		Currently the only supported values 'flags' are:
		0 = DEFAULT
		1 = COSMETIC
	*/
	uint8_t flags;

	if(header.dataPresenceFlags & SEANIM_BONE_LOC)
	{
		frame_t locKeyCount;
		BoneLocData loc[locKeyCount];
	}

	if(header.dataPresenceFlags & SEANIM_BONE_ROT)
	{
		frame_t rotKeyCount;
		BoneRotData quats[rotKeyCount];
	}

	if(header.dataPresenceFlags & SEANIM_BONE_SCALE)
	{
		frame_t scaleKeyCount;
		BoneScaleData scale[scaleKeyCount];
	}
}
```

```c++
struct BoneLocData
{
	frame_t frame;
	vec3_t loc;
}

struct BoneRotData
{
	frame_t frame;
	quat_t rot;
}

struct BoneScaleData
{
	frame_t frame;
	vec3_t scale; // 1.0 is the default scale, 2.0 is twice as big, and 0.5 is half size
}

//
// Pooled String Support Will Be Added Later Maybe
//
struct SEAnim_Note
{
	frame_t frame;
	string_t name;
}

```
