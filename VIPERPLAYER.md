# This fork

[Jetpack Media3](https://github.com/androidx/media) with the patches
[ViPER Player](https://github.com/ViPERPlayer/viperplayer) needs, kept as a **rebase** on top of
upstream `main` — never a merge.

`main` is therefore `upstream/main` plus a short, readable series of our commits. That series is the
whole point: anyone can see exactly what we changed by running

```bash
git log --oneline upstream/main..main
```

Because the branch is rebased, its history is rewritten every time it is updated. **`main` is
force-pushed**, so always `git fetch` and reset rather than merging into an old checkout.

## Current patches

- **Refactor `DefaultAudioSink` audio processor handling and float output logic** — keeps the sink on
  float output so the ViPER DSP chain receives float PCM instead of 16-bit.

## Updating onto a newer upstream

```bash
git remote add upstream https://github.com/androidx/media.git   # once
git fetch upstream
git rebase upstream/main
# resolve, then:
git push --force-with-lease origin main
```

Then bump the submodule pointer in the app:

```bash
cd ../viperplayer
git -C external/media fetch origin && git -C external/media checkout origin/main
git add external/media && git commit -m "build: update the media3 fork"
```

### Dropping patches that upstream has absorbed

Prefer deleting a patch over carrying it. When this fork was last rebased, three of its four patches
turned out to be obsolete and were dropped:

- two build workarounds (`Bump libraries`, `Fix build`) — upstream moved to convention plugins
  (`media3.android-library`) and restructured the source sets those patches were fighting;
- `Build: guard consumerProguardFiles for modules lacking the file` — upstream deleted
  `common_config.gradle`, the file it patched.

If a rebase conflicts, check whether upstream has solved the problem its own way before resolving.
Usually it has.
