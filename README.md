# OCI Image Supported Platforms

Get the OCI Platforms for a given OCI Image or Dockerfile

## Inputs

This action supports the following input.

### dockerfile

The Dockerfile to get its supported platforms

* *Required*: `No`
* *Type*: `string`
* *Default*: `Dockerfile`
* *Example*: `Dockerfile-alpine-zts`

### image

Passing anything into the `image` input will disable checking for the passed `dockerfile` input value, and will get the 
platforms for the image(s) you give it. When you pass it two or more, for example `ghcr.io/home-assistant/home-assistant:2024.12.2,ghcr.io/wyrihaximusnet/php:8.3-zts-alpine-dev`, 
it will get the platforms for all of them but only return the platforms that are supported by all of them.

* *Required*: `No`
* *Type*: `string`
* *Default*: ``
* *Example*: `ghcr.io/wyrihaximusnet/php:8.3-zts-alpine-slim`

## Output

This action only outputs a list of platforms as JSON through `platform`.

## Example

The following example build a docker image for each upstream arch:

```yaml
name: Continuous Integration
on:
  push:
jobs:
  supported-arch-matrix:
    name: Supported processor architectures
    runs-on: ubuntu-latest
    outputs:
      arch: ${{ steps.supported-arch-matrix.outputs.platform }}
    steps:
      # Note: No checkout needed, the action will handle that for you in the most optimized way possible
      - id: supported-arch-matrix
        name: Generate Arch
        uses: wyrihaximus/github-action-oci-image-supported-platforms@v1
  build-docker-image:
    name: Build ${{ matrix.platform }} image
    strategy:
      fail-fast: false
      matrix:
        platform: ${{ fromJson(needs.supported-arch-matrix.outputs.arch) }}
    needs:
      - supported-arch-matrix
    runs-on: ubuntu-latest
    steps:
      - name: Prepare
        run: |
          platform=${{ matrix.platform }}
          echo "PLATFORM_PAIR=${platform//\//-}" >> $GITHUB_ENV
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - uses: actions/checkout@v4
      - run: docker image build --platform=${{ matrix.platform }} -t "vendor/repo:${{ env.PLATFORM_PAIR }}" --no-cache .
```

For more details on how to take it from there, please have a look at [this blog post](https://blog.wyrihaximus.net/2024/10/building-secure-images-with-github-actions/).

## License ##

Copyright 2024 [Cees-Jan Kiewiet](http://wyrihaximus.net/)

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the "Software"), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
