<div align="center">

<p align="center">
  <img src="assets/logos/universities.svg" alt="National University of Singapore · Tsinghua University · University of Chinese Academy of Sciences · University of Science and Technology of China" width="100%">
</p>

<h2 align="center">Gamer: A Generalist Vision-Language-Action Foundation Model for Long-Horizon, Real-Time Game Agents</h2>

<p>
Run&nbsp;Luo · Kuan&nbsp;Zhang · Qi&nbsp;Deng · Yukun&nbsp;Chen · Zhihao&nbsp;Yang · Yufei&nbsp;Zhang · Renrong&nbsp;Hu ·  Yi&nbsp;Zhang  <br> Yinhe&nbsp;Zheng · Xiaobo&nbsp;Xia · Dawei&nbsp;Jiang · Shaoqing&nbsp;Ren
</p>

<p align="center">
  <img src="assets/logos/mihoyo.svg" alt="miHoYo" width="100" align="middle"> &nbsp; <b> Honkai AI R&amp;D Team</b>
</p>

<!-- Fill in the empty href values with the corresponding release URLs. -->
<p align="center">
  <!-- <a href=""><img src="assets/badges/models.svg" alt="Models"></a> -->
  <a href=""><img src="assets/badges/daily-paper.svg" alt="Daily Paper"></a>
  <a href=""><img src="assets/badges/project-page.svg" alt="Project Page"></a>
  <a href=""><img src="assets/badges/arxiv.svg" alt="arXiv"></a>
</p>

<p>
<a href="#abstract">Abstract</a> &nbsp;|&nbsp;
<a href="#highlights">Highlights</a> &nbsp;|&nbsp;
<a href="#todo-list">TODO List</a> &nbsp;|&nbsp;
<a href="#video-demos">Video Demos</a> &nbsp;|&nbsp;
<a href="#method-overview">Method Overview</a> &nbsp;|&nbsp;
<a href="#main-results">Main Results</a> &nbsp;|&nbsp;
<a href="#streaming-vllm">Streaming-vLLM</a> &nbsp;|&nbsp;
<a href="#system-prompt">System Prompt</a> &nbsp;|&nbsp;
<a href="#action-format">Action Format</a> &nbsp;|&nbsp;
<a href="#license">License</a> &nbsp;|&nbsp;
<a href="#citation">Citation</a>
</p>

</div>

<p align="center">
  <img src="assets/gamer-overview.png" alt="Gamer: a generalist game agent combining screen observations, short-term history, long-term memory, and reasoning to generate keyboard and mouse actions across diverse games." width="100%">
</p>

<h3 align="center">
If you find Gamer useful, please upvote our <a href="">Daily Paper</a> on Hugging Face.<br>
Thank you for your support!
</h3>

## Abstract

We introduce Gamer, a generalist agent framework built for long-horizon tasks in complex interactive environments. Its end-to-end architecture combines perception, memory management, and multi-step reasoning. A shared training and deployment pipeline supports interaction with both conventional 2D graphical interfaces and complex 3D simulated worlds. We first continue pre-training on a dataset of tens of thousands of hours of device-level keyboard and mouse interaction. These trajectories span simulated games and embodied environments, allowing the model to learn environmental dynamics across both physical and virtual domains. Sparse-thinking instruction tuning then substantially reduces redundant computation while preserving the reasoning needed for complex decisions. We train the policy for long-horizon interaction with a stable multi-turn reinforcement learning method that addresses finite context windows, error accumulation during rollouts, and sparse rewards. For real-time interaction, a cloud–edge inference engine serves numerous simulated game environments concurrently, supports unbounded-context execution, and keeps online inference latency low. Across multiple challenging 3D open-world games, Gamer generalizes well and achieves operational efficiency comparable to that of skilled players. It also achieves competitive results on a range of conventional 2D game benchmarks. These results suggest that even repetitive keyboard and mouse trajectories can support substantial gains from scaling data and learning algorithms. Training on these trajectories helps Gamer learn complex skills more steadily in environments with both physical and digital properties. This approach offers a path toward generalist agents that can interact across digital and physical worlds.

## Highlights

- **Unified Visual Control.** Gamer observes screen images and acts through native keyboard and mouse inputs, using the same interface across games.
- **Sparse Reasoning & Persistent Memory.** A single policy reasons at key decision points and uses memory summaries to retain task progress across context windows.
- **Long-Horizon Reinforcement Learning.** Temporal-Aware GRPO (TA-GRPO) trains the policy on trajectory segments linked by memory summaries, using verifiable environment feedback and temporal credit assignment.
- **Real-Time Streaming Inference.** Streaming-vLLM combines persistent context reuse, verified speculative decoding, and early action dispatch to support online rollouts, evaluation, and deployment.

## TODO List

- [x] **Paper release**
- [x] **System prompt and action format documentation**
- [x] **Gameplay demos and project page**
- [ ] **Streaming-vLLM**

## Video Demos

<!-- Replace each empty href with its video URL and update the preview image when ready. -->
<table>
  <tr>
    <th align="center" width="50%">Long-Horizon Quest Execution</th>
    <th align="center" width="50%">Puzzle Solving &amp; Real-Time Interaction</th>
  </tr>
  <tr>
    <td align="center"><a href=""><img src="assets/video-demo-placeholder.svg" alt="Long-Horizon Quest Execution — video link to be added" width="100%"></a></td>
    <td align="center"><a href=""><img src="assets/video-demo-placeholder.svg" alt="Puzzle Solving and Real-Time Interaction — video link to be added" width="100%"></a></td>
  </tr>
  <tr>
    <td align="center">Navigation, dialogue, and quest progression with persistent task memory.</td>
    <td align="center">Rule interpretation, key decisions, and continuous keyboard and mouse control.</td>
  </tr>
</table>

## Method Overview

Gamer is trained in three stages: continued pre-training (CPT), supervised fine-tuning (SFT), and online reinforcement learning (RL). All three use the same interface for screen observations and keyboard and mouse actions.

| Stage | Supervision | What the policy learns |
|---|---|---|
| **Continued Pre-Training** | Screen observations aligned with device-level actions from diverse games, combined with general multimodal data | Transferable visual control through a unified action representation |
| **Sparse-Reasoning SFT** | Instructions at the operation, subgoal, and task levels, with reasoning and memory annotations | Task-conditioned actions, reasoning at key decision points, and memory summaries that preserve progress across context windows |
| **Temporal-Aware GRPO** | Online rollouts with rewards based on verifiable environment outcomes | Improved long-horizon execution through memory-linked segments and temporal credit assignment |

During interaction, the policy uses the game's control rules, the task instruction, recent observations and actions, and persistent memory to choose its next action. At routine steps, it acts directly; when needed, it first generates reasoning or updates memory. Memory summaries retain completed objectives, failed attempts, and remaining plans across context windows.

Streaming-vLLM provides a shared inference backend for online RL rollouts, evaluation, and deployment. The policy receives visual observations and task context. The environment backend separately uses engine-side state to initialize tasks and verify rewards. See Sections 2–5 of the paper for the full method.

## Main Results

Gamer's performance improves from CPT to SFT and further with online RL on held-out tasks in **Honkai: Star Rail**. The evaluation covers two side quests with 30 and 55 milestones, respectively, plus 40 minigame instances across seven game types. Side-quest scores measure successful milestone execution. The minigame score is the macro-average across the seven types.

| Evaluation | Gamer-CPT | Gamer-SFT | Gamer-RL |
|---|---:|---:|---:|
| Opportunity Makes the Thief (Part I) | 34.4% | 72.2% | **80.0%** |
| Bestial Ferocity | 47.9% | 69.1% | **83.6%** |
| Minigame Benchmarks (macro-average) | 15.0% | 39.2% | **49.7%** |

Compared with SFT, TA-GRPO increases the two side-quest scores by **7.8** and **14.5 percentage points**, respectively, and the minigame macro-average by **10.5 percentage points**.

## Streaming-vLLM

**Coming soon.** The implementation and usage instructions will be released in the [streaming-vllm](streaming-vllm/) directory in a future update.

Streaming-vLLM is Gamer's serving engine for sustained, real-time interaction. It supports online rollout collection, evaluation, and deployment through the same observation–action protocol.

- **Persistent context and KV-cache reuse.** For each environment, Streaming-vLLM retains recent history, memory summaries, and KV-cache state. Memory compaction preserves useful task information while reusing cached context. Incremental RoPE refresh adjusts the retained keys when their positions shift.
- **Verified FSM speculative decoding.** A finite-state machine (FSM) proposes predictable structural tokens in the output format, which the target model verifies in BF16. The model generates the action values.
- **Early action dispatch.** The server sends the first verified action block as soon as it is available. Execution can then begin while the remaining blocks are still being generated.
- **Concurrent, architecture-aware serving.** Streaming-vLLM maintains separate state for each environment and schedules requests around action deadlines to serve multiple game clients concurrently. It supports Qwen3-VL and Qwen3.5 dense and MoE backbones, with execution paths tailored to each architecture.

### Inference Efficiency

For the **NVIDIA H200 / Qwen3.5-35B-A3B MoE** configuration, cumulative runtime optimizations reduce end-to-end request latency from **1306.00 ms to 65.12 ms**, yielding a **20.1× speedup**.

| Configuration | Tensor parallelism | Request latency ↓ |
|---|---:|---:|
| Baseline | TP1 | 1306.00 ms |
| Full cumulative optimization (ours) | TP4 | **65.12 ms** |

## System Prompt

The system prompt specifies the game, its rules, the available controls, and when the agent should reason before acting.

Fill in the following three placeholders in the template:

| Placeholder | Content |
|---|---|
| `{GAME_NAME}` | Game name, used in both the opening sentence and the controls heading. |
| `{GAME_RULES}` | Description of the environment, visible information, objectives, and scoring rules. |
| `{KEY_BINDINGS}` | One `- <key> -> <effect>` entry per control, using the case-sensitive key names listed below. |

For games that use the mouse to aim or rotate the camera, include this entry in the key bindings:

```text
- Mouse X Y -> aim / rotate the camera (X>0 right, Y>0 down)
```

Replace only the placeholders in the template below; preserve its output tags, punctuation, section headings, and line breaks. The asterisks in headings such as `**Output Format**` are part of the prompt text.

<details>
<summary><b>Full system prompt template</b></summary>

```text
You are a gaming expert, and you are currently playing {GAME_NAME}. You are proficient in keyboard and mouse operations. You understand game mechanics and combat pacing, can quickly extract key information from the current screen, and can think and make precise decisions at critical moments. Based on the current screen, plan the next 200ms of actions, consisting of 6 steps spaced 33ms apart. Each step lasts 33ms, until the next step begins. If the current situation continues the previous strategy, output the action directly. Think only when the situation changes significantly, the previous analysis is no longer valid, or a new objective appears.
**Game Rules**
{GAME_RULES}
**Common Keys in {GAME_NAME}**
{KEY_BINDINGS}
**Output Format**
**Action only (usual)**
<think></think><|action_start|>X Y Z ; k1 k2 k3 ; k4 k5 ; k6 ; k7 ; k8 ; k9 k10<|action_end|>
**Thinking + action (when necessary)**
<think>reasoning</think><|action_start|>X Y Z ; k1 k2 k3 ; k4 k5 ; k6 ; k7 ; k8 ; k9 k10<|action_end|>
**Explanation**
1. **Mouse Movement**: First, specify the relative displacement X, Y (X>0 means move right, Y>0 means move down) and scroll amount Z (Z>0 means scroll up).
2. **Key Sequence**: Then list 6 groups of keys; within each group, keys are separated by spaces, and groups are separated by semicolons.
- Each group can contain up to 4 keys.
- If a group has no keys, leave it empty but keep the `;`.
3. Only output a plain string that conforms to the above format — no line breaks and no quotation marks.
**Key Naming Rules**
- Number keys `0-9`: use lowercase English words, e.g., `zero` for `0`, `one` for `1`, ... `nine` for `9`.
- Function keys `F1-F12`: use capitalized English words, e.g., `One` represents `F1`, `Two` represents `F2`, and so on.
- Mouse buttons: use `LB`, `RB`, `MB` for the left, right, and middle mouse button.
- Letters: use the uppercase letter, e.g., `A`, `D`, `W`, `S`.
- Arrow keys: use `Up`, `Down`, `Left`, `Right`.
- Modifier/special keys: `Shift`, `Ctrl`, `Alt`, `Tab`, `Caps`, `Esc`, `Space`, `Enter`, `Back` (backspace), `Delete`, `Insert`, `Home`, `End`, `Pause`.
```

</details>

## Action Format

Each decision covers approximately **200 ms** of interaction, divided into **six steps of approximately 33 ms** each. Mouse displacement and scrolling apply to the entire action chunk.

```text
<|action_start|>X Y Z ; g1 ; g2 ; g3 ; g4 ; g5 ; g6<|action_end|>
```

| Field | Meaning |
|---|---|
| `X`, `Y` | Relative mouse displacement for the full chunk, measured in thousandths of the screen width and height, respectively. Each value is clipped to `[-1000, 1000]`. Positive `X` moves right; positive `Y` moves down. |
| `Z` | Mouse-wheel scroll notches, clipped to `[-5, 5]`. Positive values scroll up. |
| `g1` … `g6` | Keys held during each step. Each group contains at most four space-separated keys. An empty group releases all keys; its semicolon separator must remain. |

Convert mouse displacement to pixels with `dx = X / 1000 × screen_width` and `dy = Y / 1000 × screen_height`. A key appearing in consecutive groups stays held across those steps.

### Sparse Reasoning

The agent can act directly when its previous strategy still applies, or reason when the situation changes or a new objective requires it.

**Action only:**

```text
<think></think><|action_start|>0 0 0 ; W ; W ; W ; W ; W ; W<|action_end|>
```

**Thinking followed by action (format template):**

```text
<think>reasoning</think><|action_start|>X Y Z ; g1 ; g2 ; g3 ; g4 ; g5 ; g6<|action_end|>
```

The action-only example holds `W` throughout the chunk without moving or scrolling the mouse.

### Key Names

The action vocabulary contains 74 case-sensitive tokens:

| Input | Tokens |
|---|---|
| Letter keys | `A`–`Z` |
| Number keys | `zero` `one` `two` `three` `four` `five` `six` `seven` `eight` `nine` |
| Function keys | `One` `Two` `Three` `Four` `Five` `Six` `Seven` `Eight` `Nine` `Ten` `Eleven` `Twelve` (F1–F12) |
| Mouse buttons | `LB` `RB` `MB` |
| Arrow keys | `Up` `Down` `Left` `Right` |
| Other keys | `Shift` `Ctrl` `Alt` `Tab` `Caps` `Esc` `Space` `Enter` `Back` `Delete` `Insert` `Home` `End` `Pause` `Equal` `Minus` `Period` `Slash` `Quote` |

### Parsing and Serving

When serving the model and parsing its outputs:

- Set `skip_special_tokens: false` to retain the action delimiters.
- Parse the action after the final `</think>` tag to avoid executing format examples included in the reasoning.
- Treat incomplete reasoning or an unparseable response as a no-op.
- The action parser also accepts actions without outer delimiters and ignores unknown key tokens.

For more details on usage, see our [GameScaling](https://github.com/THUSI-Lab/GameScaling).

## License

The code and documentation in this repository are licensed under the [Apache License 2.0](LICENSE).

Game screenshots, videos, names, logos, and other third-party assets belong to their respective owners and retain their original licensing terms. The Apache-2.0 license does not grant rights to those assets.

## Citation

If you find our work useful, please cite:

**Gamer**

```bibtex
@misc{gamer,
  title = {Gamer: A Generalist Vision-Language-Action Foundation Model for Long-Horizon, Real-Time Game Agents},
  author = {Luo, Run and Zhang, Kuan and Deng, Qi and Chen, Yukun and Yang, Zhihao and Zhang, Yufei and Hu, Renrong and Zhang, Yi and Zheng, Yinhe and Xia, Xiaobo and Jiang, Dawei and Ren, Shaoqing},
  year = {2026}
}
```

**GameScaling**

```bibtex
@article{zhang-scaling-games,
  title = {Scaling in Games: Continued Pre-Training for Embodied Agents in Diverse Virtual Worlds},
  author = {Zhang, Kuan and Chen, Yukun and Yang, Zhihao and Su, Yue and Wu, Xiangnan and Chen, Zirong and Luo, Run and Hou, Jinkun and Tan, Tao and Zheng, Yinhe and Li, Yiming},
  journal = {arXiv preprint arXiv:2610.28698},
  year = {2026}
}
```
