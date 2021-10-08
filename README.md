# How `git log -S` and `git log -G` Work

## Overview

This repository shows how to use `git log -S` and `git log -G`.

## The Example File

The file `some-state-parks-in-pa.md` is created and edited in the following commits (`git log --no-show-signature`):

```
commit cc7fe6f34fbda843fea68d2efb4d6b023a99fc3b (HEAD -> master)
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:30:21 2021 -0400

    Remove `Lake Erie State Park` because it's not in PA

commit 950b473e93413f5114a92cabaed8618065a174f6
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:29:12 2021 -0400

    Reorder the state parks alphabetically

commit 5d018d6020fa3b6800b7d04c649c96b42f706610
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:27:59 2021 -0400

    Add more state parks in Pennsylvania

commit 0b3c32d34bf3fa188650cadbeea249cb924bdd31
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:23:45 2021 -0400

    Add some state parks in Pennsylvania

    (Just a few of them. Not all of them.)
```

Among all the state parks that are added to this list, the **"Lake Erie State Park"** is used specifically to show how these two `git log` options work. Note that "Lake Erie State Park" no longer exists in the current file because of the commit `cc7fe6f` which removes it because it's not a park in PA but in NY.

## How to Use `git log -S`

As [1] says:

> Look for differences that change the number of occurrences of the specified string (i.e. addition/deletion) in a file. Intended for the scripter's use.

What it means is: `git log -S<word>` only lists the commits in which the keyword `<word>` is **added** or **deleted** in a file. If `<word>` is moved inside the same file in a commit, this commit won't be listed.

[3] explains it as follows:

> "-S\<block of text\>" detects `filepairs` whose `preimage` and `postimage` have different number of occurrences of the specified block of text. By definition, it will not detect in-file moves. Also, when a changeset moves a file wholesale without affecting the interesting string, `diffcore-rename` kicks in as usual, and `-S` omits the `filepair` (since the number of occurrences of that string didn't change in that rename-detected `filepair`).

For example, running `git log --no-show-signature -SErie --stat` in this repository lists the following commits:

```
commit cc7fe6f34fbda843fea68d2efb4d6b023a99fc3b (HEAD -> master)
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:30:21 2021 -0400

    Remove `Lake Erie State Park` because it's not in PA

 some-state-parks-in-pa.md | 1 -
 1 file changed, 1 deletion(-)

commit 5d018d6020fa3b6800b7d04c649c96b42f706610
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:27:59 2021 -0400

    Add more state parks in Pennsylvania

 some-state-parks-in-pa.md | 6 ++++++
 1 file changed, 6 insertions(+)
```

The commit `5d018d6` is listed because the keyword `Erie` is **added** in this commit. In other words, the number of occurrences of "Erie" in the file "some-state-parks-in-pa.md" **changes** from 0 to 1 in this commit.

The commit `cc7fe6f` is listed because the keyword `Erie` is **deleted** in this commit. In other words, the number of occurrences of "Erie" in the file "some-state-parks-in-pa.md" **changes** from 1 to 0 in this commit.

Although the keyword `Erie` also appears in the commit `950b473`, this commit is not listed because the keyword "Erie" is simply moved from one line to another line thus the number of occurrences of "Erie" in the file "some-state-parks-in-pa.md" remains **unchanged**.

## How to Use `git log -G`

As [2] says:

> Look for differences whose patch text contains added/removed lines that match \<regex\>.

What it means is: `git log -G<regex>` lists the commits whose content changes include the keyword that matches `<regex>`.

For example, running `git log --no-show-signature -GErie --stat` in this repository lists the following commits:

```
commit cc7fe6f34fbda843fea68d2efb4d6b023a99fc3b (HEAD -> master)
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:30:21 2021 -0400

    Remove `Lake Erie State Park` because it's not in PA

 some-state-parks-in-pa.md | 1 -
 1 file changed, 1 deletion(-)

commit 950b473e93413f5114a92cabaed8618065a174f6
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:29:12 2021 -0400

    Reorder the state parks alphabetically

 some-state-parks-in-pa.md | 14 +++++++-------
 1 file changed, 7 insertions(+), 7 deletions(-)

commit 5d018d6020fa3b6800b7d04c649c96b42f706610
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:27:59 2021 -0400

    Add more state parks in Pennsylvania

 some-state-parks-in-pa.md | 6 ++++++
 1 file changed, 6 insertions(+)
```

Note that it lists `cc7fe6f` and `5d018d6` which are also listed by `git log -S` but for a different reason: "Erie" occurs in the `diff` output of the two commits:

```diff
commit cc7fe6f34fbda843fea68d2efb4d6b023a99fc3b (HEAD -> master)
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:30:21 2021 -0400

    Remove `Lake Erie State Park` because it's not in PA

diff --git a/some-state-parks-in-pa.md b/some-state-parks-in-pa.md
index 87139fe..1bb06fc 100644
--- a/some-state-parks-in-pa.md
+++ b/some-state-parks-in-pa.md
@@ -3,7 +3,6 @@
 Cherry Springs State Park
 Keystone State Park
 Kinzua Bridge State Park
-Lake Erie State Park
 Leonard Harrison State Park
 Linn Run State Park
 McConnells Mill State Park

commit 5d018d6020fa3b6800b7d04c649c96b42f706610
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:27:59 2021 -0400

    Add more state parks in Pennsylvania

diff --git a/some-state-parks-in-pa.md b/some-state-parks-in-pa.md
index 4001bf4..41eb83f 100644
--- a/some-state-parks-in-pa.md
+++ b/some-state-parks-in-pa.md
@@ -6,3 +6,9 @@ Keystone State Park
 McConnells Mill State Park
 Moraine State Park
 Ohiopyle State Park
+Linn Run State Park
+Kinzua Bridge State Park
+Cherry Springs State Park
+Leonard Harrison State Park
+Presque Isle State Park
+Lake Erie State Park
```

The commit `950b473` is also listed because its `diff` output contains the keyword "Erie":

```diff
commit 950b473e93413f5114a92cabaed8618065a174f6
Author: Yaobin Wen <robin.wyb@gmail.com>
Date:   Thu Oct 7 19:29:12 2021 -0400

    Reorder the state parks alphabetically

diff --git a/some-state-parks-in-pa.md b/some-state-parks-in-pa.md
index 41eb83f..87139fe 100644
--- a/some-state-parks-in-pa.md
+++ b/some-state-parks-in-pa.md
@@ -1,14 +1,14 @@
 # Some State Parks in Pennsylvania

-Point State Park
-Raccoon Creek State Park
+Cherry Springs State Park
 Keystone State Park
+Kinzua Bridge State Park
+Lake Erie State Park
+Leonard Harrison State Park
+Linn Run State Park
 McConnells Mill State Park
 Moraine State Park
 Ohiopyle State Park
-Linn Run State Park
-Kinzua Bridge State Park
-Cherry Springs State Park
-Leonard Harrison State Park
 Presque Isle State Park
-Lake Erie State Park
+Point State Park
+Raccoon Creek State Park
```

## References

- [1] [git log -S<string>](https://git-scm.com/docs/git-log#Documentation/git-log.txt--Sltstringgt)
- [2] [git log -G<regex>](https://git-scm.com/docs/git-log#Documentation/git-log.txt--Gltregexgt)
- [3] [diffcore-pickaxe: For Detecting Addition/Deletion of Specified String](https://git-scm.com/docs/gitdiffcore#_diffcore_pickaxe_for_detecting_additiondeletion_of_specified_string)
- [4] [Adding `git log -S` to your code detective toolbox](https://blog.danlew.net/2019/02/19/git-log-s/)
