# 7-Zip on GitHub
7-Zip website: [7-zip.org](https://7-zip.org)

## This is a Fork of 7-Zip

This fork includes two additional flags: `-slb` and `-snf`. If you don't need these features, you should use the official binaries instead.

* `-slb`: Extract the first *N* bytes of each file to stdout
* `-snf`: Print a filename header to stdout before each file (use `{name}` as a placeholder)

The `-slb` flag is useful when you only need to read the first bytes of a file, for example, to inspect a file header without fully extracting it, or to determine an image's resolution. This is typically much faster than extracting the entire file.

The real speedup depends on the archive format and how it was created. It is usually very large for ZIP and RAR archives, and for non-solid 7z archives, but much smaller for solid 7z archives because the decoder still has to walk more of the compression stream.

The `-snf` flag allows you to prepend each file's output with its filename, which is helpful when streaming multiple files to stdout and needing to distinguish between them.

For example, the following command extracts the first 64 bytes of each file from `archive.7z` and prints them to stdout, prefixed with a header containing the filename:

```sh
./7zc x -so -slb64 -snf'<<<FILE:{name}>>>' -- archive.7z image5.png image4.png image3.png
```

This will produce an output stream like:

```none
<<<FILE:image3.png>>><first 64 bytes of image3.png><<<FILE:image4.png>>><first 64 bytes of image4.png><<<FILE:image5.png>>><first 64 bytes of image5.png>
```

> [!NOTE]
> The output order may differ from the input file list, depending on how 7-Zip processes the archive.


## Benchmark:

1 folder, 137 files, 1389970597 bytes (1326 MiB), (Approx 10 MiB per image)

| Format | Original version | Modified version (First 64 bytes) | Improvement |
|--|--|--|--|
| ZIP -r 0 | 2.08s | 9.46ms | 219x |
| ZIP -r 9 | 5.42s | 60.63ms | 89x |
| RAR -m0 | 2.30s | 8.04ms | 286x |
| RAR -m5 | 6.57s | 157.15ms | 41x |
| 7z -mx=0 | 2.11s | 8.07ms | 261x |
| 7z -mx=9 | 13.28s | 12.44s | 1.06x |