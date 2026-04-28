# 7-Zip on GitHub
7-Zip website: [7-zip.org](https://7-zip.org)

## This is a Fork of 7-Zip

This fork includes two additional flags: `-slb` and `-snf`. If you don't need these features, you should use the official binaries instead.

* `-slb`: Extract the first *N* bytes of each file to stdout
* `-snf`: Print a filename header to stdout before each file (use `{name}` as a placeholder)

The `-slb` flag is useful when you only need to read the first bytes of a file, for example, to inspect a file header without fully extracting it, or to determine an image's resolution. This is typically much faster than extracting the entire file.

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
