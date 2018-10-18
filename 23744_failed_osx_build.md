This is what i did:

OSX build failed on `isis-ndw1170` with error:
**BeamlineTest.ComponentInfoTest.test_merge_identical_interval_when_rotations_differ**
Error: Expected (std::string(e.what()) == "Cannot merge ComponentInfo: " "matching scan interval but " "rotations differ"), found ("Cannot merge ComponentInfo: matching scan interval but positions differ" != Cannot merge ComponentInfo: matching scan interval but rotations differ)

So I tried to fix it like so:

This is the relevant part of the commit diff
(full commit [here](https://github.com/mantidproject/mantid/pull/23744/commits/a1474adec98f2a5bdadd7b235075f92a74f92923))

![diff 1](https://github.com/nvaytet/notes/blob/master/images/diff1.png)

I fixed `ComponentInfo &c` to use its own `rootIndex` (before it was wrongly re-using the one from `a`) and I also set the position of the zeroth component to `{1,1,1}` via `a.setPosition(0, Eigen::Vector3d{1, 1, 1});` and `c.setPosition(0, Eigen::Vector3d{1, 1, 1});`

This fixed the build (which ran again on `isis-ndw1170`).
But I wanted to understand what had actually done the fix: the `rootIndex` stuff or the `setPosition`?
So I removed the `setPosition`

![diff 2](https://github.com/nvaytet/notes/blob/master/images/diff2.png)

and the build still passed on `isis-ndw1170`.

Reverting the `rootIndex` stuff back broke the build

![diff 3](https://github.com/nvaytet/notes/blob/master/images/diff3.png)

So I thought ok it must be the `rootIndex`.

I replaced all the `rootIndex` with `a.root()` and `c.root()`, thinking this would also work

![diff 4](https://github.com/nvaytet/notes/blob/master/images/diff4.png)

but that failed on `isis-ndw1170`.

Changing the syntax to be `auto rootIndexA = a.root();` and `a.setRotation(rootIndexA, rot1);`

![diff 5](https://github.com/nvaytet/notes/blob/master/images/diff5.png)

passed the OSX build on `ill-mac`. Unfortunately it did no run on `isis-ndw1170`.

I am wondering if it is machine-specific, but maybe you can see a good reason why one cannot use `a.root()` directly inside `setRotation`? 
