---
layout: default
title: Sbatch Builder
permalink: /sbatch/
use_pyscript: true
---

<section class="tool-hero" markdown="0">
  <div class="container tool-hero-shell">
    <p class="tool-kicker">Interactive Utility</p>
    <h1 class="tool-title">Build `sbatch` scripts in the browser</h1>
    <p class="tool-summary">
      This page uses PyScript in the browser to generate a ready-to-submit Slurm batch script.
      Fill in the job settings, review the generated file, then copy or download it as a `.sbatch` file.
    </p>
  </div>
</section>

<section class="centre-section" markdown="0">
  <div class="container tool-layout">
    <article class="tool-card">
      <div class="tool-card-body">
        <h2>Job Settings</h2>
        <div class="tool-form-grid">
          <label class="tool-field">
            <span class="tool-label">Job Name</span>
            <input class="tool-input" id="job-name" type="text" value="climate-run">
          </label>
          <label class="tool-field">
            <span class="tool-label">Partition</span>
            <input class="tool-input" id="partition" type="text" value="compute">
          </label>

          <label class="tool-field">
            <span class="tool-label">Time Limit</span>
            <input class="tool-input" id="time-limit" type="text" value="08:00:00">
          </label>
          <label class="tool-field">
            <span class="tool-label">Memory</span>
            <input class="tool-input" id="memory" type="text" value="16G">
          </label>

          <label class="tool-field">
            <span class="tool-label">Nodes</span>
            <input class="tool-input" id="nodes" type="number" min="1" value="1">
          </label>
          <label class="tool-field">
            <span class="tool-label">Tasks</span>
            <input class="tool-input" id="ntasks" type="number" min="1" value="1">
          </label>

          <label class="tool-field">
            <span class="tool-label">CPUs Per Task</span>
            <input class="tool-input" id="cpus-per-task" type="number" min="1" value="4">
          </label>
          <label class="tool-field">
            <span class="tool-label">GPUs</span>
            <input class="tool-input" id="gpus" type="text" placeholder="Leave empty if not needed">
          </label>

          <label class="tool-field">
            <span class="tool-label">Account</span>
            <input class="tool-input" id="account" type="text" placeholder="lab_account">
          </label>
          <label class="tool-field">
            <span class="tool-label">QoS</span>
            <input class="tool-input" id="qos" type="text" placeholder="normal">
          </label>

          <label class="tool-field tool-field-full">
            <span class="tool-label">Working Directory</span>
            <input class="tool-input" id="workdir" type="text" value="$HOME/projects/carisma">
            <span class="tool-help">The script will `cd` here before running your commands.</span>
          </label>

          <p class="tool-section-title tool-field-full">Output And Notifications</p>

          <label class="tool-field">
            <span class="tool-label">Standard Output File</span>
            <input class="tool-input" id="stdout-file" type="text" value="logs/%x-%j.out">
          </label>
          <label class="tool-field">
            <span class="tool-label">Error File</span>
            <input class="tool-input" id="stderr-file" type="text" value="logs/%x-%j.err">
          </label>

          <label class="tool-field">
            <span class="tool-label">Mail Type</span>
            <select class="tool-select" id="mail-type">
              <option value="">No email</option>
              <option value="END,FAIL" selected>END,FAIL</option>
              <option value="ALL">ALL</option>
              <option value="FAIL">FAIL</option>
              <option value="BEGIN,END,FAIL">BEGIN,END,FAIL</option>
            </select>
          </label>
          <label class="tool-field">
            <span class="tool-label">Mail User</span>
            <input class="tool-input" id="mail-user" type="email" placeholder="name@example.org">
          </label>

          <p class="tool-section-title tool-field-full">Environment</p>

          <label class="tool-field tool-field-full">
            <span class="tool-label">Modules To Load</span>
            <input class="tool-input" id="modules" type="text" value="python/3.11 gcc/12.2">
            <span class="tool-help">Separate multiple modules with spaces.</span>
          </label>

          <label class="tool-field">
            <span class="tool-label">Conda Environment</span>
            <input class="tool-input" id="conda-env" type="text" placeholder="climate-tools">
          </label>
          <label class="tool-field">
            <span class="tool-label">Launch Command</span>
            <input class="tool-input" id="launch-command" type="text" value="srun python run_model.py">
          </label>

          <label class="tool-field tool-field-full">
            <span class="tool-label">Commands Before Launch</span>
            <textarea class="tool-textarea" id="pre-commands">mkdir -p logs
echo "Running on $(hostname)"
python --version</textarea>
          </label>

          <label class="tool-field tool-field-full">
            <span class="tool-label">Commands After Launch</span>
            <textarea class="tool-textarea" id="post-commands" placeholder="Optional cleanup or staging commands"></textarea>
          </label>
        </div>

        <div class="tool-actions">
          <button class="btn btn-primary" py-click="generate_sbatch">Generate Script</button>
          <button class="btn btn-outline" py-click="load_example">Load GPU Example</button>
        </div>
        <p class="tool-status" id="tool-status">Ready.</p>
      </div>
    </article>

    <aside class="tool-card">
      <div class="tool-card-body">
        <div class="tool-output-title">
          <h2>Generated Script</h2>
          <span class="tool-badge">PyScript</span>
        </div>
        <pre class="tool-output"><code id="sbatch-output">Generating initial script...</code></pre>
        <div class="tool-output-actions">
          <button class="btn btn-secondary" py-click="copy_script">Copy</button>
          <button class="btn btn-outline" py-click="download_script">Download</button>
        </div>

        <div class="tool-section-title">Notes</div>
        <div class="tool-notes">
          <p class="tool-note">This generator runs entirely in the browser. It does not submit jobs to Slurm for you.</p>
          <p class="tool-note">Cluster-specific keys such as `account`, `partition`, `qos`, GPU syntax, or module names may need adjustment for your HPC system.</p>
          <p class="tool-note">If your site uses a different launcher, replace `srun` with the command expected by your cluster.</p>
        </div>
      </div>
    </aside>
  </div>

  <script type="py">
from pyscript import document, window
from js import Blob, URL


def field_value(field_id):
    return document.getElementById(field_id).value.strip()


def set_field(field_id, value):
    document.getElementById(field_id).value = value


def set_status(message):
    document.getElementById("tool-status").textContent = message


def add_directive(lines, flag, value):
    if value:
        lines.append(f"#SBATCH {flag}={value}")


def build_script_text():
    job_name = field_value("job-name") or "job"
    partition = field_value("partition")
    time_limit = field_value("time-limit")
    memory = field_value("memory")
    nodes = field_value("nodes") or "1"
    ntasks = field_value("ntasks") or "1"
    cpus_per_task = field_value("cpus-per-task") or "1"
    gpus = field_value("gpus")
    account = field_value("account")
    qos = field_value("qos")
    workdir = field_value("workdir")
    stdout_file = field_value("stdout-file")
    stderr_file = field_value("stderr-file")
    mail_type = field_value("mail-type")
    mail_user = field_value("mail-user")
    modules = field_value("modules")
    conda_env = field_value("conda-env")
    launch_command = field_value("launch-command") or "srun hostname"
    pre_commands = field_value("pre-commands")
    post_commands = field_value("post-commands")

    lines = [
        "#!/bin/bash",
        f"#SBATCH --job-name={job_name}",
    ]

    add_directive(lines, "--partition", partition)
    add_directive(lines, "--time", time_limit)
    add_directive(lines, "--mem", memory)
    add_directive(lines, "--nodes", nodes)
    add_directive(lines, "--ntasks", ntasks)
    add_directive(lines, "--cpus-per-task", cpus_per_task)
    add_directive(lines, "--gpus", gpus)
    add_directive(lines, "--account", account)
    add_directive(lines, "--qos", qos)
    add_directive(lines, "--output", stdout_file)
    add_directive(lines, "--error", stderr_file)
    add_directive(lines, "--mail-type", mail_type)
    add_directive(lines, "--mail-user", mail_user)

    body = [
        "",
        "set -euo pipefail",
        "",
        'echo "Job ${SLURM_JOB_ID} started on $(date)"',
    ]

    if workdir:
        body.extend(["", f'cd "{workdir}"'])

    if modules:
        body.extend(["", "module purge", f"module load {modules}"])

    if conda_env:
        body.extend(
            [
                "",
                'source "$(conda info --base)/etc/profile.d/conda.sh"',
                f"conda activate {conda_env}",
            ]
        )

    if pre_commands:
        body.extend(["", "# Setup", pre_commands])

    body.extend(["", "# Main workload", launch_command])

    if post_commands:
        body.extend(["", "# Post-processing", post_commands])

    body.extend(["", 'echo "Job ${SLURM_JOB_ID} finished on $(date)"'])
    return "\n".join(lines + body), job_name


def render_script():
    script_text, _ = build_script_text()
    document.getElementById("sbatch-output").textContent = script_text
    return script_text


def generate_sbatch(event=None):
    render_script()
    set_status("Script regenerated from the current form values.")


def load_example(event=None):
    set_field("job-name", "downscale-gpu")
    set_field("partition", "gpu")
    set_field("time-limit", "12:00:00")
    set_field("memory", "64G")
    set_field("nodes", "1")
    set_field("ntasks", "1")
    set_field("cpus-per-task", "8")
    set_field("gpus", "1")
    set_field("account", "climate_lab")
    set_field("qos", "accelerated")
    set_field("workdir", "$HOME/projects/downscaling")
    set_field("stdout-file", "logs/%x-%j.out")
    set_field("stderr-file", "logs/%x-%j.err")
    set_field("mail-type", "BEGIN,END,FAIL")
    set_field("mail-user", "researcher@example.org")
    set_field("modules", "cuda/12.2 python/3.11")
    set_field("conda-env", "torch-env")
    set_field("launch-command", "srun python train_downscaler.py --config configs/gpu.yaml")
    set_field("pre-commands", 'mkdir -p logs\nnvidia-smi\npython -c "import torch; print(torch.cuda.is_available())"')
    set_field("post-commands", "python collect_metrics.py")
    render_script()
    set_status("GPU example loaded.")


async def copy_script(event=None):
    script_text = render_script()
    await window.navigator.clipboard.writeText(script_text)
    set_status("Script copied to the clipboard.")


def download_script(event=None):
    script_text, job_name = build_script_text()
    blob = Blob.new([script_text], {"type": "text/plain;charset=utf-8"})
    url = URL.createObjectURL(blob)
    anchor = document.createElement("a")
    anchor.href = url
    anchor.download = f"{job_name}.sbatch"
    document.body.appendChild(anchor)
    anchor.click()
    anchor.remove()
    URL.revokeObjectURL(url)
    set_status(f'Downloaded "{job_name}.sbatch".')


render_script()
  </script>
</section>
