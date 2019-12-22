# BPF Typewriter

This is a [bpftrace] program to run locally that turns your keyboard into a noisy typewriter. Keystrokes make a "click" and the enter key makes a "ting".

This works by tracing the Linux tty driver using kprobes. I first wrote tracing-typewriter tools in [2005].

## Dependencies

- [bpftrace]
- alsa-utils for the `aplay` command. Don't want alsa-utils? Edit the source to use a different audio player.

## Source

Just the code:

```
#!/usr/local/bin/bpftrace --unsafe
#include <uapi/linux/input-event-codes.h>

kprobe:kbd_event
/arg1 == EV_KEY && arg3 == 1/
{
	if (arg2 == KEY_ENTER) {
		system("aplay -q clink.au >/dev/null 2>&1 &");
	} else {
		system("aplay -q click.au >/dev/null 2>&1 &");
	}
}

```

I would have traced kbd_rawcode(), but it appears inlined and unavailable to kprobes.

[bpftrace]: https://github.com/iovisor/bpftrace
[2005]: http://www.brendangregg.com/specials.html#typewriter
