# @itk-wasm/dam

Data Archive Manager (dam): a tool to prepare and download source code repository testing, example, and website asset data.

Data is stored in .tar.gz archives and can be hosted at multiple, redundant https URLs. A simpler, more flexible alternative to Git LFS, etc.

## Features

- Download and unpack testing and example data .tar.gz archives
- CLI for NPM scripts
- Verify with an expected IPFS content identifier (CID), reproducible across IPFS implementations with the [unixfs-v1-2025](https://specs.ipfs.tech/ipips/ipip-0499/) profile
- Cache archive downloads based on the expected hash
- Support for multiple storage urls
- Automatic retries

## Installation

```sh
npm install -g @itk-wasm/dam
```

## Usage

Store your data in a directory. For example:

```sh
> mkdir -p test/data
> touch test/data/file1.txt
> touch test/data/file2.txt
```

Pack the current version of the data into a compressed archive:

```sh
> dam pack test/data test/data.tar.gz
CID: bafkreibetv3747ysuplkqkwlomeaao3nk3uak7wtzgrgmrtvmah5jdyawy
```

Upload the *test/data.tar.gz* file to hosting services such as an IPFS pinning service, GitHub Releases, AWS s3, a local http server, etc.

The CID is computed with the [IPIP-499](https://specs.ipfs.tech/ipips/ipip-0499/) `unixfs-v1-2025` profile (CIDv1, sha2-256, raw leaves, 1 MiB chunks, 1024 links per node), so IPFS implementations that use the same profile produce the same CID for the archive. For example, with [Kubo](https://github.com/ipfs/kubo) 0.40 or newer:

```
> ipfs config profile apply unixfs-v1-2025
> ipfs add test/data.tar.gz
added bafkreibetv3747ysuplkqkwlomeaao3nk3uak7wtzgrgmrtvmah5jdyawy data.tar.gz
```

In your NPM scripts, add

```sh
> dam download -v test/data test/data.tar.gz bafkreibetv3747ysuplkqkwlomeaao3nk3uak7wtzgrgmrtvmah5jdyawy https://w3s.link/ipfs/bafkreibetv3747ysuplkqkwlomeaao3nk3uak7wtzgrgmrtvmah5jdyawy
```

If the data is stored at multiple locations, add all URLs to the end of the `download` subcommand.

The first time this command is called the data will be downloaded and unpacked.

When data updates are required, repeat the pack / upload process, and update the content identifer (CID) hash.
