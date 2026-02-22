# PR #3: Add META_MUTATION flag for mutation node evaluation ordering

## Why This PR?

This PR addresses an issue where nodes that modify other nodes via side-effects may evaluate in undefined order relative to their consumers.

### The Problem

In the engine-sim project, some nodes modify other nodes:
- `AddSampleNode` adds samples to a `FunctionNode.m_samples` vector
- `FunctionNode.generate()` uses those samples

Piranha's default evaluation order is based on the dependency graph, but mutation nodes and their targets are often at the same dependency level. This leads to undefined evaluation order.

**Original behavior**: Worked by accident on Windows due to container ordering.

**Problem exposed**: Different compiler/linker ordering on other platforms caused the mutation nodes to evaluate after consumers, resulting in empty data.

## Changes

### 1. Add META_MUTATION flag (`include/node.h`)
```cpp
static constexpr const char *META_MUTATION = "!!PIRANHA::MUTATION";
```

### 2. Two-pass evaluation (`src/node_program.cpp`)
- Phase 1: Evaluate all mutation nodes first
- Phase 2: Evaluate remaining nodes

## Usage

Nodes that modify other nodes via side-effects should add the flag:

```cpp
class AddSampleNode : public Node {
public:
    AddSampleNode() {
        addFlag(Node::META_MUTATION);
    }
};
```

## Backward Compatibility

- **Fully backward compatible**
- Only affects nodes with `META_MUTATION` flag
- Existing nodes without the flag behave identically
- Opt-in feature

## Testing

- Tested with engine-sim project
- Mutation nodes now evaluate before consumers
- All existing tests behave identically

## Alternative Considered

Restructuring engine-sim to use explicit dependencies instead of side-effects was considered but would require:
- Significant refactoring
- Breaking all existing `.mr` script files
- 2-4 weeks of effort

This feature provides a minimal, opt-in solution.
