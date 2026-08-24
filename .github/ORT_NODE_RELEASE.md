# Custom ONNX Runtime Node package

This fork is based on the official ONNX Runtime `v1.29.0` release. Its Node.js binding adds the following session option:

```ts
const session = await ort.InferenceSession.create(modelPath, {
  enableMemReuse: false,
});
```

The `Build and publish custom ORT Node` workflow follows the official Node.js execution-provider matrix:

- Linux x64: CPU, CUDA, TensorRT, and WebGPU
- Linux arm64: CPU
- Windows x64 and arm64: CPU, DirectML, and WebGPU
- macOS x64: CPU
- macOS arm64: CPU, CoreML, and WebGPU

The Linux x64 core is built with the generic CUDA and TensorRT provider interfaces. As in the official
`onnxruntime-node` package, its CUDA, TensorRT, and shared provider libraries are downloaded from the matching
`Microsoft.ML.OnnxRuntime.Gpu.Linux` NuGet package during npm installation instead of being bundled in the tarball.
The package's provider metadata pins those libraries to the base ONNX Runtime version.

Windows WebGPU ARM64 cross-compilation reuses the `llvm-tblgen.exe` and `clang-tblgen.exe` host tools produced by
the Windows x64 build, matching the official packaging pipeline. Windows builds pin CMake 3.31.6 and macOS builds
pin CMake 3.31.8, using each platform's upstream workflow version and SHA512 so legacy third-party CMake projects
remain configurable.

## Repository setup

The workflow publishes `@chthollyphile/onnxruntime-node-folia`.

1. Create a GitHub environment named `npm`. Add required reviewers if releases need approval.
2. If the package does not exist on npm yet, add a granular npm access token with publish access as the repository
   secret `NPM_TOKEN`, then run the workflow once with `publish` enabled.
3. In the npm package settings, add a trusted publisher with these values:
   - Organization or user: `chthollyphile`
   - Repository: `onnxruntime`
   - Workflow filename: `ort-node-release.yml`
   - Environment: `npm`
4. Remove `NPM_TOKEN` after trusted publishing is working.

The package metadata is rewritten during the packaging job so that its repository URL matches this fork. No npm
credential is stored in the repository.

## Build or release

- Run the workflow manually with `publish` disabled to build all six native targets and download the resulting `.tgz`
  artifact without publishing it.
- Run it manually with `publish` enabled to publish the requested version and npm dist-tag.
- Push a tag such as `ort-node-v1.29.0-memreuse.0` to build and publish that version automatically.

The tag or manual version must be valid npm SemVer. A release tag publishes with the `latest` npm dist-tag; use manual
dispatch when a different dist-tag is required.
