This is what i did:

OSX build failed with error:
**BeamlineTest.ComponentInfoTest.test_merge_identical_interval_when_rotations_differ**
Error: Expected (std::string(e.what()) == "Cannot merge ComponentInfo: " "matching scan interval but " "rotations differ"), found ("Cannot merge ComponentInfo: matching scan interval but positions differ" != Cannot merge ComponentInfo: matching scan interval but rotations differ)

So I tried to fix it like so:

This is the relevant part of the commit diff
(full commit [here](https://github.com/mantidproject/mantid/pull/23744/commits/a1474adec98f2a5bdadd7b235075f92a74f92923))

![diff 1](https://github.com/nvaytet/notes)

I fixed `ComponentInfo &c` to use its own `rootIndex` (before it was wrongly re-using the one from `a`)
