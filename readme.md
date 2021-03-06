Introduction
============
https://github.com/elliotwoods/VVVV.Packs.OpenCV

This pack defines a new Image type for VVVV which lets you work with images on the CPU (previously achieved with DirectShow filters and FreeFrame).
CVImageLink assists automatic threading, double buffering, conversion, and simple OpenCV development

The VVVV.Nodes.OpenCV plugin defines the CVImageLink type, and introduces a base set of functionality for working with it. 

The other plugins in this pack work with the CVImageLink type, generally generating, filtering or detecting something with it.

Elliot Woods (www.kimchiandchips.com)

This fork is attempting to make image pack VPM friendly, by splitting each smaller projects in this pack into its own package. So the less savvvvy can use an up-to-date and both x86 and x64 version.

Credits
=======
Thanks for vux, alg and Elias. Thanks also to the other core VVVV developers and users.

Thanks to Lumacoustics of London, for their continued support in sponsoring development of the OpenCV plugins.

License
=======
This plugin is distributed under the MIT license

Parts of this pack may reference EmguCV which is distributed under a GPL license.
If you wish to use these parts without the GPL restrictions, then I suggest paying for a commercial EmguCV license for this project.
Buying said license (if bought for the VVVV.Packs.OpenCV project) will also indemnify any other users of this project from the EmguCV license conditions.

Installation
============

If you are installing this pack for development here are the steps you need to take:
* clone this repo to be in vvvvXY\packs folder 
* make sure you also have the DX11 pack in vvvvXY\packs\DX11 as there are some references to this location
* make sure you also have the addonpack installed
* make sure you're using nuget >= 2.8.x 
* open src\VVVV.Packs.CV.sln 
* make sure Build Platform is set to: x86
* Build

With VisualStudio 2013 (latest update):
* never click the option "Enable NuGet Packet Restore" in the Solution menu!

With SharpDevelop 5 (beta5):
* when building initially fails run "Restore Packages" in the Solution menu and build again

Updating nugets:
If for some reason you want to update nugets to build against vvvv-alpha nugets, go ahead. In the nugets dialog simply choose "Update all". Special care only needs to be taken in SharpDevelop 5 (beta 5) where after doing so all nuget references have their "Local Copy" set to true which will break things. So after updating first have a look at the changes to all .csproj files and restore the deleted PRIVATE=false lines. 

Usage
=====

Types
-----
We are now using CVImageLink which is a polymorphic (accepts different formats of image), double threaded (handles threading, locking) and auto converting (e.g. for RGBA8 for GPU) image type.

To use this type yourself, check out examples of where we use CVImageInputSpread and CVImageInputSpreadWith<T>

It can be used for lots of things. We can make nodes in minutes to supply video from lots of sources (e.g. CLEye, Point Grey, BlackMagic) and then use them with the full chain of CV / texture utils.


Threading
---------
Current design is 'Highly Threaded' or 'Background' as described in the threading options list at
http://vvvv.org/forum/replacing-directshow-with-managed-opencv.-video-playback-capture-cv

Each node has it's own thread, every exchange of image data between threads results in a double buffer
This obviously leads to many threads + much memory usage (perhaps in the future users will be able to select the global threading model at runtime)

The node thread has an output buffer (single).
When the thread is ready to send (i.e. all of its inputs are fresh) then it calls FOutputBuffer.SetImage(...) which pushes the image downstream.
FOutput should not have any internal buffers, instead passing through its input directly

Links are double buffers


Memory usage notes
------------

Examples of memory usage:

* 640*480 ~= 300KB (VGA mono)
* 640*480*3 ~= 1MB (VGA colour)
* 640*480*16 ~= 5MB (VGA colour + alpha, 32bit float)
* 1920*1080*3 ~= 6MB (HD colour video frame)

Generally each slice at each node = 2 * the above (double buffered)
