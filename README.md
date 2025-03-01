# `llm` - Large Language Models for Everyone, in Rust

`llm` is an ecosystem of Rust libraries for working with large language models -
it's built on top of the fast, efficient [GGML](./crates/ggml) library for
machine learning.

![A llama riding a crab, AI-generated](./doc/img/llm-crab-llama.png)

> _Image by [@darthdeus](https://github.com/darthdeus/), using Stable Diffusion_

[![Latest version](https://img.shields.io/crates/v/llm.svg)](https://crates.io/crates/llm)
![MIT/Apache2](https://shields.io/badge/license-MIT%2FApache--2.0-blue)
[![Discord](https://img.shields.io/discord/1085885067601137734)](https://discord.gg/YB9WaXYAWU)

The primary entrypoint [for developers](#using-llm-in-a-rust-project) is
[the `llm` crate](./crates/llm), which wraps [`llm-base`](./crates/llm-base) and
the [supported model](./crates/models) crates.
[Documentation](https://docs.rs/llm) for released version is available on
Docs.rs.

For end-users, there is [a CLI application](#using-the-llm-cli),
[`llm-cli`](./binaries/llm-cli), which provides a convenient interface for
interacting with supported models. [Text generation](#running) can be done as a
one-off based on a prompt, or interactively, through
[REPL or chat](#does-the-llm-cli-support-chat-mode) modes. The CLI can also be
used to serialize (print) decoded models,
[quantize](./crates/ggml/README.md#quantization) GGML files, or compute the
[perplexity](https://huggingface.co/docs/transformers/perplexity) of a model. It
can be downloaded from
[the latest GitHub release](https://github.com/rustformers/llm/releases) or by
installing it from `crates.io`.

`llm` is powered by the [`ggml`](https://github.com/ggerganov/ggml) tensor
library, and aims to bring the robustness and ease of use of Rust to the world
of large language models. At present, inference is only on the CPU, but we hope
to support GPU inference in the future through alternate backends.

Currently, the following models are supported:

- [BLOOM](https://huggingface.co/docs/transformers/model_doc/bloom)
- [GPT-2](https://huggingface.co/docs/transformers/model_doc/gpt2)
- [GPT-J](https://huggingface.co/docs/transformers/model_doc/gptj)
- [GPT-NeoX](https://huggingface.co/docs/transformers/model_doc/gpt_neox)
  (includes [StableLM](https://github.com/Stability-AI/StableLM),
  [RedPajama](https://www.together.xyz/blog/redpajama), and
  [Dolly 2.0](https://www.databricks.com/blog/2023/04/12/dolly-first-open-commercially-viable-instruction-tuned-llm))
- [LLaMA](https://huggingface.co/docs/transformers/model_doc/llama) (includes
  [Alpaca](https://crfm.stanford.edu/2023/03/13/alpaca.html),
  [Vicuna](https://lmsys.org/blog/2023-03-30-vicuna/),
  [Koala](https://bair.berkeley.edu/blog/2023/04/03/koala/),
  [GPT4All](https://gpt4all.io/index.html), and
  [Wizard](https://github.com/nlpxucan/WizardLM))
- [MPT](https://www.mosaicml.com/blog/mpt-7b)

## Using `llm` in a Rust Project

This project depends on Rust v1.65.0 or above and a modern C toolchain.

The `llm` crate exports `llm-base` and the model crates (e.g. `bloom`, `gpt2`
`llama`).

Add `llm` to your project by listing it as a dependency in `Cargo.toml`. To use
the version of `llm` you see in the `main` branch of this repository, add it
from GitHub (although keep in mind this is pre-release software):

```toml
[dependencies]
llm = { git = "https://github.com/rustformers/llm" , branch = "main" }
```

To use a [released](https://github.com/rustformers/llm/releases) version, add it
from [crates.io](https://crates.io/crates/llm) by specifying the desired
version:

```toml
[dependencies]
llm = "0.1"
```

**NOTE**: To improve debug performance, exclude the transitive `ggml-sys`
dependency from being built in debug mode:

```toml
[profile.dev.package.ggml-sys]
opt-level = 3
```

## Using the `llm` CLI

The easiest way to get started with `llm-cli` is to download a pre-built
executable from a [released](https://github.com/rustformers/llm/releases)
version of `llm`, although this may not have all the features present on the
`main` branch. The following methods involve building `llm`, which requires Rust
v1.65.0 or above and a modern C toolchain.

### Installing with `cargo`

To install the most recently released version of `llm` to your Cargo `bin`
directory, which `rustup` is likely to have added to your `PATH`, run:

```shell
cargo install llm-cli
```

The CLI application can then be run through `llm`.

### Building from Source

To make use of the features on the `main` branch, clone the repository and then
build it with

```shell
git clone --recurse-submodules git@github.com:rustformers/llm.git
cargo build --release
```

The resulting binary will be at `target/release/llm[.exe]`.

It can also be run directly through Cargo, with

```shell
cargo run --release -- $ARGS
```

## Getting Models

GGML files are easy to acquire. For a list of models that have been tested, see
the [known-good models](./doc/known-good-models.md).

Certain older GGML formats are not supported by this project, but the goal is to
maintain feature parity with the upstream GGML project. For problems relating to
loading models, or requesting support for
[supported GGML model types](https://github.com/ggerganov/ggml#roadmap), please
[open an Issue](https://github.com/rustformers/llm/issues/new).

### From Hugging Face

Hugging Face 🤗 is a leader in open-source machine learning and hosts hundreds
of GGML models.
[Search for GGML models on Hugging Face 🤗](https://huggingface.co/models?search=ggml).

### r/LocalLLaMA

This Reddit community maintains
[a wiki](https://www.reddit.com/r/LocalLLaMA/wiki/index/) related to GGML
models, including well organized lists of links for acquiring
[GGML models](https://www.reddit.com/r/LocalLLaMA/wiki/models/) (mostly from
Hugging Face 🤗).

## Usage

Once the `llm` executable has been built or is in a `$PATH` directory, try
running it. Here's an example that uses the open-source
[GPT4All](https://gpt4all.io/models/ggml-gpt4all-j-v1.3-groovy.bin) language
model:

```shell
llm llama infer -m ggml-gpt4all-j-v1.3-groovy.bin -p "Rust is a cool programming language because"
```

For more information about the `llm` CLI, use the `--help` parameter.

There is also a [simple inference example](./crates/llm/examples/inference.rs)
that is helpful for [debugging](./.vscode/launch.json):

```shell
cargo run --release --example inference llama ggml-gpt4all-j-v1.3-groovy.bin $OPTIONAL_PROMPT
```

## Q&A

### Does the `llm` CLI support chat mode?

Yes, but certain fine-tuned models (e.g.
[Alpaca](https://crfm.stanford.edu/2023/03/13/alpaca.html),
[Vicuna](https://lmsys.org/blog/2023-03-30-vicuna/),
[Pygmalion](https://docs.alpindale.dev/)) are more suited to chat use-cases than
so-called "base models". Here's an example of using the `llm` CLI in REPL
(Read-Evaluate-Print Loop) mode with an Alpaca model - note that the
[provided prompt format](./utils/prompts/alpaca.txt) is tailored to the model
that is being used:

```shell
llm llama repl -m ggml-alpaca-7b-q4.bin -f utils/prompts/alpaca.txt
```

There is also a [Vicuna chat example](./crates/llm/examples/vicuna-chat.rs) that
demonstrates how to create a custom chatbot:

```shell
cargo run --release --example vicuna-chat llama ggml-vicuna-7b-q4.bin
```

### Can `llm` sessions be persisted for later use?

Sessions can be loaded (`--load-session`) or saved (`--save-session`) to file.
To automatically load and save the same session, use `--persist-session`. This
can be used to cache prompts to reduce load time, too.

### How do I use `llm` to quantize a model?

`llm` can produce a `q4_0`- or
`q4_1`-[quantized](./crates/ggml/README.md#quantization) model from an
`f16`-quantized GGML model

```shell
cargo run --release $MODEL_ARCHITECTURE quantize $MODEL_IN $MODEL_OUT {q4_0,q4_1}
```

### Do you provide support for Docker and NixOS?

The `llm` [Dockerfile](./utils/Dockerfile) is in the `utils` directory, as is a
[NixOS flake](./utils/flake) manifest and lockfile.

### What's the best way to get in touch with the `llm` community?

GitHub [Issues](https://github.com/rustformers/llm/issues/new) and
[Discussions](https://github.com/rustformers/llm/discussions/new) are welcome,
or come chat on [Discord](https://discord.gg/YB9WaXYAWU)!

### Do you accept contributions?

Absolutely! Please see the [contributing guide](./doc/CONTRIBUTING.md).

### What applications and libraries use `llm`?

#### Applications

- [llmcord](https://github.com/rustformers/llmcord): Discord bot for generating
  messages using `llm`.
- [local.ai](https://github.com/louisgv/local.ai): Desktop app for hosting an
  inference API on your local machine using `llm`.

#### Libraries

- [llm-chain](https://github.com/sobelio/llm-chain): Build chains in large
  language models for text summarization and completion of more complex tasks
