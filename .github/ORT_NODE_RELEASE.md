# Custom ONNX Runtime Node package

This fork is based on the official ONNX Runtime `v1.29.0` release. Its Node.js binding adds the following session option:

```ts
const session = await ort.InferenceSession.create(modelPath, {
  enableMemReuse: false,
});
```

The `Build and publish custom ORT Node` workflow builds one CPU package containing native binaries for:

- Linux x64 and arm64
- Windows x64 and arm64
- macOS x64 and arm64

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
