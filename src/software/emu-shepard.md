# Testing Emu with Shepard

This guide goes through how to set up Emu and Shepard so you can test them
together on your own machine.

It covers what each project is, how to set each one up, and how to test them
with each other.

## Shepard

### What is Shepard?

[Shepard](./shepard.md) is our onboard scripting software, which runs on our
drones. For Emu, Shepard is what lets the drone talk to the ground station: it
connects the drone's hardware to Emu.

Shepard runs a small web server that Emu connects to. Status messages and
logs are sent over a WebSocket (at `/ws`), and images are served at
`/images`.

Repository: [github.com/uaarg/shepard](https://github.com/uaarg/shepard)

### Setting Up Shepard

1. Clone the repository:

   ```sh
   git clone git@github.com:uaarg/shepard.git
   cd shepard
   ```

2. Initialize all submodules:

   ```sh
   git submodule update --init --recursive
   ```

3. Create a virtual environment and install the dependencies:

   ```sh
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```

That's it. The [Shepard README](https://github.com/uaarg/shepard#how-do-i-get-set-up)
has more setup steps (e.g. auto-running on a Raspberry Pi), but you don't need
them if you only want to test Emu and Shepard together.

## Emu

### What is Emu?

Emu is our custom ground station software. It monitors the drone's status,
displays images and logs messages. It is updated every year as our needs for
competition change.

Repository: [github.com/uaarg/emu](https://github.com/uaarg/emu)

### Setting Up Emu

Follow the setup instructions in the
[Emu README](https://github.com/uaarg/emu#readme). In short, you install
the dependencies, then run the server and the frontend in two separate
terminals:

```sh
./scripts/install.sh   # Linux or macOS. See the README for Windows
npm run server         # terminal 1
npm run dev            # terminal 2
```

## Testing Emu and Shepard Together

### Preparing Emu

If you have made changes to Emu and tested them on their own (e.g. checked
that the UI works), you don't need to do anything else to Emu before testing
it with Shepard.

### Preparing Shepard

Shepard's main logic is written as Python scripts, so the easiest way to test
is to write a Python script that exercises the features you want to test.

The `samples/` directory has many examples you can base your test script on.
If you're unsure how something works in Shepard, look there first. For
example, `samples/emu_connection.py` connects to Emu and sends it logs and
images:

```python
from src.modules.emu import Emu
import time

emu = Emu("res")  # images are served from this directory
emu.start_comms()
time.sleep(2)

emu.send_log("hello from shepard", "normal")  # "normal", "warning" or "error"
emu.send_image("test-image.jpeg")
```

Run samples from the root of the Shepard repository with the helper script,
which sets up `PYTHONPATH` for you:

```sh
samples/run.sh emu_connection
```

Run `samples/run.sh` with no arguments to list all available samples.

### Running the Test

1. Start your Shepard script (as above). It starts the server that Emu
   connects to, which listens on port `8080` by default.
2. Start Emu's server and frontend, and open the frontend in your browser.
3. In Emu, enter Shepard's address (e.g. `http://127.0.0.1:8080`) in the
   connection field and connect.

You should now see Shepard's logs and images appear in Emu. Test away!

> NOTE: Shepard only serves images that are inside the directory passed to
> `Emu(...)` (e.g. `res/`). If an image doesn't show up in Emu, check that the
> file is in that directory.
