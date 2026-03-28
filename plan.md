The user experiences `OSError: [Errno 22] Invalid argument` when setting a static color other than white for their Razer Pro Type Ultra in Polychromatic.
This is because `openrazer_daemon` writes 3 bytes (RGB) to `/sys/.../matrix_effect_static`, but `razerkbd_driver.c` only accepts 1 byte for the Pro Type Ultra variants (`count != 1`).

To fix this, we should change `razerkbd_driver.c` so that:
1. It expects 3 bytes for `matrix_effect_static` on the Pro Type Ultra models to maintain sysfs API compatibility with `openrazer_daemon`.
2. It derives the equivalent `brightness` or `color intensity` or just a static value (like `0x01`) from the RGB buffer to pass to `razer_pro_type_matrix_effect_static`. Wait, since it's a monochromatic white keyboard, what did `buf[0]` do before?
