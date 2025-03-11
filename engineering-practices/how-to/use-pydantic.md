# Pydantic: Examples of Usage
**Source: [Discourse](https://discourse.canonical.com/t/pydantic-examples-of-usage/2800)**

## Environment
* [Pydantic](https://docs.pydantic.dev/latest/) 2.5.2
* Python 3.11.4

## What it is?
A data validation library for Python.

## How to install?
```bash
python -m venv venv
source venv/bin/activate
pip install pydantic 
```

## Examples of Usage

For all the examples, is considered a class named `Constraint` that represents a [Juju Machine Constraint](https://juju.is/docs/juju/constraint).

### Simple validations

This example validates:
- arch is required and should be `amd64`, `arm64`, `ppc64el`, `s390x` or `riscv64` (case sensitive)
- cores is a required int > 0
- root_disk is a required int > 0
- container is not required, default is `lxd` and should be `lxd` or `kvm` (case sensitive)

```python
from datetime import datetime, timezone
from typing import Annotated, Literal
from annotated_types import Gt
from pydantic import BaseModel, ValidationError, create_model, ConfigDict, Field

class Constraint(BaseModel):
    arch: Literal['amd64', 'arm64', 'ppc64el', 's390x', 'riscv64']
    cores: Annotated[int, Gt(0)]
    root_disk: Annotated[int, Gt(0)]
    container: Literal['lxd','kvm'] = 'lxd'

# here happens "validation": "process of instantiating a model (or other type)
# that adheres to specified types and constraints."
first_constraint = Constraint(
        arch='amd64',
        cores=1,
        root_disk=1
    )

print(first_constraint.model_dump(mode='json')) # print in JSON
print(first_constraint.model_json_schema()) # generates JSON schema
```

Note: Validation can be made in four ways: `BaseModel`, `pydantic.dataclasses.dataclass`, `TypeAdapter` and `validate_call`. See ["Dataclasses, TypedDicts, and more"](https://docs.pydantic.dev/latest/why/#dataclasses-typeddict-more) for more details about it.

### Error handling

```python
def print_errors(e):
    errors = [
        f"Error in '{error['loc'][0]}' option: '{error['msg']}'"
        for error in e.errors()
    ]
    errorstrs = "\n".join(errors)
    print(errorstrs)

bad_constraint = dict(
    arch='foo',
)

try:
    Constraint(**bad_constraint)
except ValidationError as e:
     print_errors(e)
```

Output:
```bash
Error in 'arch' option: 'Input should be 'amd64', 'arm64', 'ppc64el', 's390x' or 'riscv64''
Error in 'cores' option: 'Field required'
Error in 'root_disk' option: 'Field required'
```

### Extending a model

```python
ConstraintImageID = create_model(
    'ConstraintImageID',
    image_id=(str, ...),  # ... indicates that is required field
    __base__=Constraint,
)
second_constraint = ConstraintImageID(
        arch='amd64',
        cores=1,
        root_disk=1,
        image_id="ubuntu-bf2-v2"
    )
print(second_constraint.model_dump(mode='json'))  # print in JSON
```

### Making a model immutable

```python
class Constraint(BaseModel):
    model_config = ConfigDict(frozen=True)

    arch: Literal['amd64', 'arm64', 'ppc64el', 's390x', 'riscv64']
    cores: Annotated[int, Gt(0)]
    root_disk: Annotated[int, Gt(0)]
    container: Literal['lxd','kvm'] = 'lxd'

first_constraint = Constraint(
        arch='amd64',
        cores=1,
        root_disk=1
    )

try:
    first_constraint.arch = 'arm64'
except ValidationError as e:
    print_errors(e)
```

Output:
```bash
Error in 'arch' option: 'Instance is frozen'
```

### Creating dynamic default values

```python
def datetime_now() -> str:
    now = datetime.now(timezone.utc)
    return now.strftime("%Y%m%d")


class ConstraintWithTags(ConstraintImageID):
    tags: datetime = Field(default_factory=datetime_now) # note that we are using Field now


third_constraint = ConstraintWithTags(
        arch='amd64',
        cores=1,
        root_disk=1,
        image_id="ubuntu-bf2-v2"
    )

print(third_constraint.model_dump(mode='json'))  # print in JSON
```

Output:
```bash
{'arch': 'amd64', 'cores': 1, 'root_disk': 1, 'container': 'lxd', 'image_id': 'ubuntu-bf2-v2', 'tags': '20231206'}
```

["The Field function is used to customize and add metadata to fields of models."](https://docs.pydantic.dev/latest/concepts/fields/)


