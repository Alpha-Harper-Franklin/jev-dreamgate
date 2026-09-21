# DreamGate

**Decide when a world model needs to imagine again.**

DreamGate will study semantic triggers for expensive world-model and World Action Model (WAM) calls. A lightweight supervisor will decide whether to reuse a valid plan, request new observations, or invoke another prediction/replanning step. Jev is the first intended semantic backend.

**Status: design-stage project.** This repository publishes the hypothesis, an example decision contract, and a proposed evaluation. There is no model integration, scheduler, benchmark result, or measured compute saving yet.

## Research question

Can semantic signals improve the success-versus-compute tradeoff over fixed-frequency calls and simple state-change triggers?

Candidate signals include changed task conditions, an invalidated subgoal, repeated execution failure, and uncertainty that new observations can resolve.

## Proposed architecture

```text
Current observations + execution feedback + plan validity
    -> cheap numerical checks and semantic gate
    -> reuse a still-valid action source
       OR acquire fresh observations
       OR invoke world model / WAM / replanner
```

The gate is evaluated before an expensive call. Scoring a video after generating it does not save that generation cost. Every skipped invocation requires a valid alternative action source, such as an existing controller or an unexpired action chunk.

Jev receives a textual/structured summary. It cannot directly consume world-model latent tensors. Summary extraction time and compute belong in the evaluation budget.

`examples/gate_request.json` is a proposed project interface, not a TypeSafe API request.

## First milestone

- [ ] Implement an explicit action-source and expiry contract.
- [ ] Add fixed-frequency and numerical-change trigger baselines.
- [ ] Add a Jev gate and log every trigger, skipped call, and fallback.
- [ ] Integrate one real model and compatible evaluation environment.
- [ ] Sweep invocation budgets and publish success-versus-compute curves.

DreamZero is a candidate WAM integration, not an implemented dependency. A model must remain on its supported embodiment and task setup; adding this gate does not make it an autonomous-driving model.

## Evaluation contract

Use matched tasks, seeds, observation access, and controllers. Report task success, real GPU time, wall-clock duration, API cost, invocation counts, deadline misses, and stale-plan failures. Include perception and summarization costs. Compare fixed-frequency, numerical-trigger, and semantic-trigger policies over several budgets.

Threshold selection and final evaluation use separate episodes. Benefits must survive comparisons with a small local classifier where appropriate. No speedup or performance guarantee is claimed.

## 中文说明

DreamGate 研究世界模型与 WAM 的按需调用：已有计划有效时复用，任务条件变化时补充观察或重新预测。门控发生在昂贵推理之前；跳过调用时必须仍有有效动作来源。

当前处于设计阶段，未完成 Jev、DreamZero 或其他模型接入，也没有节省算力的实验结论。后续以相近成功率下的实际 GPU 时间和总成本为核心指标。

## Sources

- [TypeSafe models](https://docs.typesafe.ai/models)
- [TypeSafe confidence](https://docs.typesafe.ai/confidence)
- [DreamZero](https://github.com/dreamzero0/dreamzero): an existing WAM implementation.
- [World Action Models are Zero-shot Policies](https://arxiv.org/abs/2602.15922)

Independent community project; not affiliated with TypeSafe or NVIDIA. MIT licensed; upstream projects retain their own licenses.
