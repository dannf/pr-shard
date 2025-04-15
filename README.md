# pr-shard: Split up large PRs into batches by file
I often need to make a similar change across a bunch of files but, because my coe base is very active, it's difficult to make such changes without a merge conflict or some pre-existing CI failure. In those cases, it is helpful to split my changes up into smaller batches, let most of them land naturally, and then work on the others in smaller pieces. So I wrote a tool to do this for me.

## What it does
pr-shard splits the changes in a branch into multiple branches, where each branch only touches a subset of files. If you have 1 commit on your original branch that touches 100 files, pr-shard will split that into 100 branches of shard size 1, or 5 branches of shard size 20. It doesn't matter how many commits your branch has. Each shard branch will get a copy of all of the commits that touch its set of files, filtered to only touch those files.

Say you're on a branch called 'feature-x', which has 3 commits:

Fix foo.c (touches 1 file)
Clean up all header files (touches 46 files)
Clean up some header files (touches 12 files)

`pr-shard origin/main -b feature-x -s 20` would split this into 3 branches:

pr-shard/feature-x/0:
  foo.c: Fix foo.c
  header1.h: Clean up all header files
  header2.h: Clean up all header files
  ...
  header19.h: Clean up all header files
  header1.h: Clean up some header files
  header19: Clean u some header files

pr-shard/feature-x/1:
  header21.h: Clean up all header files
  header22.h: Clean up all header files
  header23.h: Clean up all header files
  ...
  header21.h: Clean up some header files
  header23.h: Clean up some header files

pr-shard/feature-x/2:
  header41.h: Clean up all header files
  header42.h: Clean up all header files
  header43.h: Clean up all header files
