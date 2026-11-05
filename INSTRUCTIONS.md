# Set this up on Windows

You are starting from a Windows laptop with nothing installed for this project. The laptop has a normal built-in graphics chip (Intel or AMD). It is not an NVIDIA GPU. Training does not run on this PC. Training runs in a browser, on a free Google Colab GPU.

Do each section yourself, in order. Stop at the end of a section and write down what the screen showed before you start the next one.

A coding agent such as Codex may sit beside you. It should ask you questions and wait. If it offers to install Python, CUDA, or Unsloth on the laptop, or to start training, tell it no and point it at `AGENTS.md`.

You are done with setup when the checklist at the bottom is true. The first training run is a short dry run on Colab, not a full job, and not on the laptop.

## What you are building

The model reads one piece of text and answers questions about it. For this project the questions are:

- Is this text a prompt injection? Yes or no.
- How severe is it? An ordered scale you define, such as none, low, high.

It returns the choice and a probability. It does not write a paragraph.

The training guide is [Train your own decision model with Unsloth](https://unsloth.ai/docs/basics/train-your-own-decision-model-with-unsloth). Each training row has three fields: `state` (the text), `questions` (what to decide, including the allowed options), and `gold` (the correct answer). Their page shows a support-ticket example. Yours is the same shape, with injection risk instead of a billing team.

The accuracy numbers on that page (about 78% holdout) are from intent datasets: typed-decisions, BANKING77, and CLINC150. They are not a score for catching attacks. Do not quote them as your result.

## 0. Confirm this laptop is not the training machine

Open Task Manager (Ctrl+Shift+Esc), go to Performance, and click GPU. Write the name in a local note. You will see something like Intel UHD, Intel Iris, or AMD Radeon. That chip draws the screen. It does not train this model.

Also write your Windows version. Press the Windows key, type `winver`, press Enter.

Do not install an NVIDIA driver. There is no NVIDIA card to drive. Do not install Miniconda, PyTorch, or Unsloth on this PC.

## 1. Accounts

Create these if you do not have them. Use your own email. Do not use someone else's keys or cloud project.

1. GitHub. The project already lives at <https://github.com/jayvpatel75/prompt-injection-decision-model>.
2. Google. Colab needs the same Google account you can sign into in the browser. This is the account that gets the free GPU.
3. Hugging Face: <https://huggingface.co/join>. Colab uses it to download the model and the datasets.
4. After you can log in to Hugging Face, create a token at <https://huggingface.co/settings/tokens>. Choose a read token. Store it in a password manager. In Colab, put it in the notebook's Secrets (the key icon), not in a code cell. Never paste the token into chat, into a file in this repo, or into a screenshot.

Done when: you can open GitHub, Google, and your Hugging Face profile in the browser, and the token is not in this folder.

## 2. Git and a place for the notes

The laptop only needs Git, so you can keep notes and later save a notebook. It does not need Python.

1. Install Git for Windows from <https://git-scm.com/download/win>. In the installer, pick "Git from the command line and also from 3rd-party software." Leave the rest on the defaults if you are unsure.
2. Close any open terminal. Open a new **Windows PowerShell** from the Start menu. Do not use Command Prompt.
3. Check Git:

```powershell
git --version
```

You want a version number, not "not recognized."

4. Clone the repo into your user folder:

```powershell
cd $env:USERPROFILE
git clone https://github.com/jayvpatel75/prompt-injection-decision-model.git
cd prompt-injection-decision-model
```

5. Confirm you are in the project:

```powershell
pwd
ls
```

You should see `INSTRUCTIONS.md` and `AGENTS.md`.

Done when: `git --version` prints a number, and `pwd` ends in `prompt-injection-decision-model`.

## 3. The coding agent

Install Codex using OpenAI's current Windows instructions. Open **this folder** as the project, not your whole user folder.

First message to paste:

```text
Read AGENTS.md. Coach me through INSTRUCTIONS.md one section at a time. Do not run commands for me. This laptop has no NVIDIA GPU. Training is on Colab. Start by asking what I have already finished.
```

If the agent starts installing CUDA, Miniconda, or Unsloth locally, stop it and repeat that message.

Done when: the agent has asked you a question and is waiting, and it has not installed anything.

## 4. Open Colab on a free GPU

1. On the [decision-model guide](https://unsloth.ai/docs/basics/train-your-own-decision-model-with-unsloth), open the Google Colab notebook. If that link is stale, use [Unsloth Studio on Colab](https://colab.research.google.com/github/unslothai/unsloth/blob/main/studio/Unsloth_Studio_Colab.ipynb). Sign in with your Google account.
2. Choose File, then Save a copy in Drive, so the notebook is yours.
3. Menu: Runtime, then Change runtime type. Set the hardware accelerator to **GPU**. Save.
4. Runtime, then Connect. Then run the first cell that checks the GPU. In a cell, this is enough:

```python
!nvidia-smi
```

Colab should print a server GPU such as a Tesla T4. That name is the machine in Google's data center. It will not be Intel or AMD, and it will not be your laptop.

If Colab says GPUs are unavailable, try again later, or use the Kaggle notebook linked from the same decision-model page. Still do not train on the laptop.

A free Colab session disconnects. Leave the tab open while a cell runs. The copy in Drive is what you keep.

Done when: `nvidia-smi` inside Colab prints a GPU name, and you can say that name is not the chip in your laptop.

## 5. Log in to Hugging Face inside Colab

In the notebook, add your read token as a Secret, not as text in a cell. The Unsloth notebook's own login cell should read that secret. After it runs, it should show your Hugging Face username.

If the notebook prints the token, or you pasted the token into a cell, delete that cell, revoke the token on the Hugging Face site, and make a new one.

Done when: the notebook shows your Hugging Face username, and a search of the notebook for `hf_` finds nothing.

## 6. Datasets you may download for training

Download these from Colab, **train** splits only. Leave them in the Colab session or the Hugging Face cache. Do not commit the files. Do not download them onto the laptop.

| Use | Link | Which split |
| --- | --- | --- |
| Harmful and benign prompts | <https://huggingface.co/datasets/allenai/wildguardmix> | config `wildguardtrain`, split `train` |
| Labeled prompt injection | <https://huggingface.co/datasets/xTRam1/safe-guard-prompt-injection> | `train` |
| Attack text, label unsafe | <https://huggingface.co/datasets/OpenSafetyLab/Salad-Data> | config `attack_enhanced_set`, split `train`, cap the rows |
| Jailbreak and ordinary chat | <https://huggingface.co/datasets/lmsys/toxic-chat> | config `toxicchat0124`, split `train` |
| Ordinary instructions | <https://huggingface.co/datasets/VMware/open-instruct> | `train`, a capped sample |
| Ordinary instructions | <https://huggingface.co/datasets/microsoft/orca-agentinstruct-1M-v1> | a few train subsets, capped |
| Ordinary chatbot prompts | <https://huggingface.co/datasets/alespalla/chatbot_instruction_prompts> | `train`, capped |
| Injection and benign prompts | <https://huggingface.co/datasets/reshabhs/SPML_Chatbot_Prompt_Injection> | `train` |
| Extra injection train rows | <https://huggingface.co/datasets/hendzh/PromptShield/resolve/main/train.json> | `train.json` only |
| Extra injection train rows | <https://raw.githubusercontent.com/leolee99/PIGuard/main/datasets/train.json> | `train.json` only |
| Jailbreak and regular prompts, not on the Hub | <https://raw.githubusercontent.com/verazuo/jailbreak_llms/main/data/prompts/jailbreak_prompts_2023_12_25.csv> and <https://raw.githubusercontent.com/verazuo/jailbreak_llms/main/data/prompts/regular_prompts_2023_12_25.csv> | capped samples |

These three are the smaller starter set if the full list is too much for a first week:

- <https://huggingface.co/datasets/deepset/prompt-injections> split `train`
- <https://huggingface.co/datasets/xTRam1/safe-guard-prompt-injection> split `train`
- <https://huggingface.co/datasets/neuralchemy/Prompt-injection-dataset> config `full`, split `train`

`xTRam1/safe-guard-prompt-injection` appears in both lists. Train on its `train` split. Do not train on its `test` split.

## 7. Rows you must not train on

Anything you train on cannot be used later to claim the model works. Hold these out:

- The `test` split of `deepset/prompt-injections`, `xTRam1/safe-guard-prompt-injection`, and `neuralchemy/Prompt-injection-dataset`
- `hendzh/PromptShield` files `validation.json` and `test.json`
- PIGuard files other than `train.json`: `valid.json`, the NotInject files, `wildguard.json`, and the BIPIA task lists
- Any file whose name says test, valid, validation, or fixture
- The Unsloth demo mix (`LocalLLaMA/typed-decisions`, BANKING77, CLINC150) if you want a security result. That mix is fine for learning the trainer. It is not your attack benchmark.

Before the first real training run, be able to say which file is for training and which file is for scoring only. If a row might be in both, drop it from training.

## 8. Setup checklist

You are finished with setup when all of these are true:

- Task Manager's GPU name is Intel or AMD, and you have not installed NVIDIA software
- `git --version` works in PowerShell, and this repo is cloned
- A Colab notebook that you saved to Drive shows a server GPU from `nvidia-smi`
- The Colab login shows your Hugging Face username, and the notebook does not contain the token
- `git status` is clean of token files, model weights, and dataset dumps
- You can explain, in a sentence, the difference between a train split and a test split for one dataset above

Do not start a multi-hour training job yet. The next session is a short dry run in that Colab notebook (`max_steps` around 60 is their quick try). Before you launch it, write down what you expect the loss to do on a yes/no decision, and how you will check that the model does not answer "safe" for every row.

## If you get stuck

Write down the exact click or command, the last 20 lines of output, and what you expected. Ask the agent what it thinks the error means only after you have said what you think.

Colab problems stay in Colab: runtime type GPU, then reconnect. The laptop install page is not your fix: <https://unsloth.ai/docs/get-started/install/windows-installation> is for an NVIDIA PC, which this is not.
