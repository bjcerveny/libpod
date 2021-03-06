# OCIcrypt Library

The `ocicrypt` library is the OCI image spec implementation of container image encryption. More details of the spec can be seen in the [OCI repository](https://github.com/opencontainers/image-spec/pull/775). The purpose of this library is to encode spec structures and consts in code, as well as provide a consistent implementation of image encryption across container runtimes and build tools.

## Usage

There are various levels of usage for this library. The main consumers of these would be runtime/buil tools, and a more specific use would be in the ability to extend cryptographic function.

### Runtime/Build tool usage

The general exposed interface a runtime/build tool would use, would be to perform encryption or decryption of layers:

```
package "github.com/containers/ocicrypt"
func EncryptLayer(ec *config.EncryptConfig, encOrPlainLayerReader io.Reader, desc ocispec.Descriptor) (io.Reader, EncryptLayerFinalizer, error)
func DecryptLayer(dc *config.DecryptConfig, encLayerReader io.Reader, desc ocispec.Descriptor, unwrapOnly bool) (io.Reader, digest.Digest, error)
```

The settings/parameters to these functions can be specified via creation of an encryption config with the `github.com/containers/ocicrypt/config` package. We note that because setting of annotations and other fields of the layer descriptor is done through various means in different runtimes/build tools, it is the resposibility of the caller to still ensure that the layer descriptor follows the OCI specification (i.e. encoding, setting annotations, etc.).


### Crypto Agility and Extensibility

The implementation for both symmetric and assymetric encryption used in this library are behind 2 main interfaces, which users can extend if need be. These are in the following packages:
- github.com/containers/ocicrypt/blockcipher - LayerBlockCipher interface for block ciphers
- github.com/containers/ocicrypt/keywrap - KeyWrapper interface for key wrapping

We note that adding interfaces here is risky outside the OCI spec is not recommended, unless for very specialized and confined usecases. Please open an issue or PR if there is a general usecase that could be added to the OCI spec.

## Security Issues

We consider security issues related to this library critical. Please report and security related issues by emailing maintainers in the [MAINTAINERS](MAINTAINERS) file.
