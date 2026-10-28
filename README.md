# doc_intake

**One capability bit from GBT V8, packaged standalone.** No. 161 of 341,
`mind` domain (Mind).

<p>
<img alt="python" src="https://img.shields.io/badge/python-3.9%2B-blue">
<img alt="license" src="https://img.shields.io/badge/license-MIT-green">
<img alt="deps" src="https://img.shields.io/badge/runtime%20dependencies-0-brightgreen">
</p>

> 知识库·**解析 → 入库 → 同步**那条链（吞 WeKnora 后蒸的另一面：本仓原来只有单件 `datakit`/`genoffice`）。三条口径：① **只吃本地文件**（出网那条路另挂门，本位不偷偷下载）② **不新造解析器**：md/txt 直读、html 只做去标签（**并如实标注不是真解析**）、其余类型如实报「解析手未接」③ `sources` 只**如实报**外部源（飞书/GitLab）接线状态，**没接就说没接**。op=intake/plan/sources/verify/status/where

That description is copied verbatim from the registry. `tests/test_spec.py` pins it,
so documentation drift fails the build instead of shipping quietly.

## What this package actually is

It is the **contract** for one capability: its name, its parameters, the values it
accepts, and whether it can change state that cannot be restored. It is not the
implementation — that lives in the runtime this bit was extracted from.

So `run()` goes through a narrow `Backend` protocol, and the default backend is a
stub that **says so**:

```
status="validated"   backend="stub"   data={"executed": false}
```

Never `status="ok"`. A stub that reports success is worse than no stub, because the
caller stops looking for the real thing.

## Install

```bash
git clone https://github.com/gbtweilai-star/gbt-doc-intake.git
cd gbt-doc-intake
python -m pip install -e ".[dev]"
```

## Use

```bash
python -m gbt_doc_intake info                  # capability card
python -m gbt_doc_intake spec --json           # machine-readable contract
python -m gbt_doc_intake check --payload '{}'
python -m gbt_doc_intake run   --payload '{}'
```

Real `info` output from this machine:

```text
doc_intake  (mind)  v1.0.0
----------------------------------------
知识库·**解析 → 入库 → 同步**那条链（吞 WeKnora 后蒸的另一面：本仓原来只有单件 `datakit`/`genoffice`）。三条口径：① **只吃本地文件**（出网那条路另挂门，本位不偷偷下载）② **不新造解析器**：md/txt 直读、html 只做去标签（**并如实标注不是真解析**）、其余类型如实报「解析手未接」③ `sources` 只**如实报**外部源（飞书/GitLab）接线状态，**没接就说没接**。op=intake/plan/sources/verify/status/where

gated      : False
external   : False
multi-op   : True
params     : (none)
values     : intake, plan, sources, verify, status, where
```

Wire a real implementation:

```python
from gbt_doc_intake import run

class MyBackend:
    name = "my-backend"
    def invoke(self, cap, payload):
        return {"cap": cap, "handled": sorted(payload)}

result = run({}, backend=MyBackend())
assert result.status == "ok"
```

## Parameters

| name | required | notes |
|---|---|---|
| — | no | the registry declares no required parameters |

Registry hint (`params_hint`): (empty)

**注册表登记的取值 / 子动作（6 个）**：`intake`、`plan`、`sources`、`verify`、`status`、`where`

Types are deliberately absent: the registry does not declare them, and an invented
type is worse than no type, because callers believe it.

## Boundaries

- Not gated: the registry marks this bit `safe: true`.
- No external service dependency declared.
- Multi-op (`multi_op: true`): several sub-actions share one name — read `op` before calling.
- No implementation ships here. Bring a backend; the protocol is one method.

## What this is not

Not a wrapper around a hosted API. Not a model. Not a promise that calling it does
anything on its own. It is the typed seam plus the validation, which is the part that
stays correct even when the implementation changes.

## Tests

```bash
python -m pytest
```

```text
20 passed, 3 skipped in 0.19s
```

## Provenance

| field | value |
|---|---|
| capability | `doc_intake` |
| domain | `mind` |
| handler | `doc_intake` |
| safe | `True` (gated = `False`) |
| external | `False` |
| atlas page | [mind/doc_intake](https://github.com/gbtweilai-star/gbt-caps/blob/main/caps/mind/doc_intake.md) |
| full index | [https://github.com/gbtweilai-star/gbt-caps](https://github.com/gbtweilai-star/gbt-caps) |

The parent README says 348 capabilities; an AST pass over the registry enumerates
`341 + 0 unimplemented + 0 disabled = 341`. The extra 7 are historical duplicates.
Numbering here follows 341 and matches the `gbt-caps` index one-to-one.

## License

MIT.
