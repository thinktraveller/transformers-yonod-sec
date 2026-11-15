# YONOD security backports for Transformers 4.57.6

This local fork identifies itself as `4.57.6+yonod.sec1`. It is not an upstream
Transformers release, and it does not impersonate version 5.x.

The base is the official `v4.57.6` annotated tag
`7dacdf4543ca4fe081f99dedacdb70d8df0fd366`, resolving to source commit
`753d61104116eefc8ffc977327b441ee0c8d599f`. The local repository initially
contains that shallow upstream snapshot; earlier upstream history is not
claimed to be present. Original Apache-2.0 notices and LICENSE are preserved.

Four upstream fixes are adapted to the 4.x implementation:

| Advisory | Upstream commit | Adaptation |
| --- | --- | --- |
| GHSA-xrqw-3rrv-vx5w | `eaaaf8494dd5386634ae37d1d122212fdc315be5` | Validate resolved parent before writing each named tokenizer or processor template. |
| GHSA-fgcw-684q-jj6r | `676559d5022b74aaa0cee1cee0842b7f27c5320e` | Resolve LightGlue nested configs through native CONFIG_MAPPING and remove propagation of serialized remote-code trust. Modular sources and generated runtime sources remain synchronized. |
| GHSA-29pf-2h5f-8g72 | `a7f8e7ff37d87d1a1a0c8cf607971c607741452f` | Ignore serialized internal attention/experts selectors and limit kernel identification to kernels-community. |
| GHSA-69w3-r845-3855 | `03c8082ba4594c9b8d6fe190ca9bed0e5f8ca396` | Apply the existing Torch safety check before RNG-state loading, and explicitly use weights_only=True. |

The 4.x base already imports pathlib.Path in both template writers and already
provides check_torch_load_is_safe. LightGlue retains the 4.x constructor and
module structure. The kernel helper retains Python 3.9-compatible annotations.
No 5.x dataclass/configuration framework is introduced.

The distribution includes `transformers/yonod_security_backports.json` and
`transformers/yonod_security_sbom.cdx.json`. The latter records declared direct
runtime dependency requirements, not a fully resolved environment or a claim
that all transitive dependencies are vulnerability-free. Built wheel hashes
and the complete candidate-environment inventory are external release evidence.

Validation uses production functions with harmless stubs and owned temporary
directories. It does not execute remote kernels, custom model code, real
pickles, model weights, descriptor calculations, training, or predictions.
Application/model compatibility and Linux/Windows execution need separate
acceptance in the isolated candidate environment.

Torch versions below the existing safety threshold are intentionally rejected
when resuming Trainer RNG files. Replacing weights_only with unrestricted
loading is not a compatibility workaround. The kernels-community allowlist is
the upstream trust boundary; it is not a sandbox or a guarantee about every
kernel published by that organization. Source-built accessory conversion
scripts remain outside the supported YONOD workflow; no claim is made that all
upstream source examples or optional dependencies have been removed or audited.
The template checks protect against untrusted template names. The chosen save
directory and local filesystem must remain trusted; the upstream fix is not a
sandbox against a prearranged directory symlink or concurrent path replacement.

Dependabot may continue matching the public version range of 4.57.6. A local
version string does not make those alerts disappear automatically. Record
patch provenance and per-advisory evidence rather than changing the version
to a fictitious patched upstream release.
