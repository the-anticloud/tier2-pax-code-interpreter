# Developer Cookbook — PAX_CODE_INTERPRETER
**Stack:** Python 3.11, RestrictedPython, subprocess (sandboxed), AIOSS_FORMAT

## Basic Usage
```python
from pax_code_interpreter import Codeinterpreter
module = Codeinterpreter(pax_model="./pax-27b-q4.gguf",
                               aioss_chain="./pax_code_interpreter.aioss")
result = module.process(input_data)
print(result.output, result.chain_hash)
```

## Batch Processing
```python
results = module.process_batch(inputs, batch_size=4)
for r in results:
    print(r.chain_hash)
```

## AIOSS Append
```python
import hashlib, time
def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()

chain_hash = aioss_append("./pax_code_interpreter.aioss", result.to_bytes(), "PAX_CODE_INTERPRETER")
```

## Integration with Anticloud TIER_2
```python
# Chain with PAX_INFERENCE_CORE
from pax_inference_core import PAXInferenceCore
from pax_code_interpreter import Codeinterpreter

core = PAXInferenceCore(model="./pax-27b-q4.gguf")
module = Codeinterpreter(inference_core=core)
```

## Domain: Sandboxed code execution for PAX 27B generated code
This module specializes in: sandboxed code execution for pax 27b generated code.
AIOSS entry type: code execution event (source hash + stdout hash + exit code + sandbox violation if any).

## Performance
Use module.benchmark() to measure throughput on your hardware.
Pre-warm: module.warmup() before serving production requests.
