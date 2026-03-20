# EXP NO: 2 Pulse-Code-Modulation & Delta Modulation
# NAME: VARSHINI M
# REGISTER NUMBER: 212224060293

# Aim
Write a simple Python program for the modulation and demodulation of PCM, and DM.
# Tools required
1.Personal Computer

2.Google Colab
# Program
# Pulse Code Modulation
```
import numpy as np
import matplotlib.pyplot as plt

# ===== Signal Parameters =====
frequency = 2        # Hz
amplitude = 1
duration = 1         # Reduced for visual clarity
analog_rate = 1000   # "Continuous" signal resolution
sample_rate = 30     # Increased to better demonstrate sampling theorem
num_levels = 8       # 3-bit quantization

# ===== 1. Analog Signal Generation =====
t_analog = np.linspace(0, duration, int(analog_rate * duration), endpoint=False)
analog_signal = amplitude * np.sin(2 * np.pi * frequency * t_analog)

# ===== 2. Sampling (Discretizing Time) =====
t_samp = np.linspace(0, duration, int(sample_rate * duration), endpoint=False)
sampled_signal = amplitude * np.sin(2 * np.pi * frequency * t_samp)

# ===== 3. Quantization (Discretizing Amplitude) =====
# Step size calculation: Δ = (Max - Min) / L
v_min, v_max = -amplitude, amplitude
step_size = (v_max - v_min) / num_levels

# Quantization logic: Mid-rise
# Find which level index each sample falls into
indices = np.floor((sampled_signal - v_min) / step_size)
indices = np.clip(indices, 0, num_levels - 1).astype(int)

# Reconstruct the signal using the midpoint of each quantization level
quantized_signal = v_min + (indices + 0.5) * step_size

# ===== 4. PCM Encoding (Binary Conversion) =====
num_bits = int(np.log2(num_levels))
binary_codes = [np.binary_repr(i, width=num_bits) for i in indices]

print(f"Quantization Levels: {num_levels} ({num_bits} bits)")
print("Sample Binary Stream (First 10):", " ".join(binary_codes[:10]))

# ===== 5. Visualization =====
fig, axes = plt.subplots(3, 1, figsize=(10, 10), sharex=True)
plt.subplots_adjust(hspace=0.4)
plt.suptitle("EXP-3 PULSE CODE MODULATION\nNAME : VARSHINI M \nREG NO : 212224060293",fontsize=12, fontweight='bold')

# Plot 1: Sampling
axes[0].plot(t_analog, analog_signal, label="Analog Signal", color='gray', alpha=0.5)
axes[0].stem(t_samp, sampled_signal, linefmt='C0-', markerfmt='C0o', label="Sampled Points")
axes[0].set_title("Step 1: Sampling (Time Discretization)")
axes[0].legend(loc='upper right')
axes[0].grid(True, alpha=0.3)

# Plot 2: Quantization

axes[1].plot(t_analog, analog_signal, 'k--', alpha=0.2)
axes[1].step(t_samp, quantized_signal, where='mid', color='C1', label="Quantized Signal")
# Draw horizontal lines for quantization levels
for level in np.linspace(v_min, v_max, num_levels + 1):
    axes[1].axhline(y=level, color='red', linestyle=':', alpha=0.2)
axes[1].set_title(f"Step 2: Quantization ({num_levels} Levels)")
axes[1].set_ylabel("Amplitude")
axes[1].legend(loc='upper right')

# Plot 3: Resulting Bitstream Visualization (Logic Representation)
axes[2].plot(t_analog, analog_signal, label="Original", alpha=0.3)
axes[2].step(t_samp, quantized_signal, where='mid', color='C2', label="PCM Output")
axes[2].set_title("Step 3: Final PCM Reconstructed Signal")
axes[2].set_xlabel("Time (s)")
axes[2].legend(loc='upper right')
axes[2].grid(True, alpha=0.3)

plt.show()

```

# DELTA MODULATION
# Program:
```
#Delta Modulation
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, filtfilt
# Parameters
fs = 10000  # Sampling frequency
f = 10  # Signal frequency
T = 1  # Duration in seconds
delta = 0.1  # Step size
t = np.arange(0, T, 1/fs)
message_signal = np.sin(2 * np.pi * f * t)  # Sine wave as input signal
# Delta Modulation Encoding
encoded_signal = []
dm_output = [0]  # Initial value of the modulated signal
prev_sample = 0
for sample in message_signal:
    if sample > prev_sample:
        encoded_signal.append(1)
        dm_output.append(prev_sample + delta)
    else:
        encoded_signal.append(0)
        dm_output.append(prev_sample - delta)
    prev_sample = dm_output[-1]
# Delta Demodulation (Reconstruction)
demodulated_signal = [0]
for bit in encoded_signal:
    if bit == 1:
        demodulated_signal.append(demodulated_signal[-1] + delta)
    else:
        demodulated_signal.append(demodulated_signal[-1] - delta)
# Convert to numpy array
demodulated_signal = np.array(demodulated_signal)
# Apply a low-pass Butterworth filter
def low_pass_filter(signal, cutoff_freq, fs, order=4):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff_freq / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return filtfilt(b, a, signal)
filtered_signal = low_pass_filter(demodulated_signal, cutoff_freq=20, fs=fs)
# Plotting the Results
plt.figure(figsize=(12, 6))
plt.subplot(3, 1, 1)
plt.plot(t, message_signal, label='Original Signal', linewidth=1)
plt.legend()
plt.grid()
plt.subplot(3, 1, 2)
plt.step(t, dm_output[:-1], label='Delta Modulated Signal', where='mid')
plt.legend()
plt.grid()
plt.subplot(3, 1, 3)
plt.plot(t, filtered_signal[:-1], label='Demodulated & Filtered Signal', linestyle='dotted', linewidth=1, color='r')
plt.legend()
plt.grid()
plt.tight_layout()
plt.show()

```
# Output Waveform
# PULSE CODE MODULATION

<img width="962" height="892" alt="image" src="https://github.com/user-attachments/assets/f9c8abfc-deea-46a9-912c-364dc6b7ec7d" />

# DELTA MODULTION

<img width="1572" height="756" alt="image" src="https://github.com/user-attachments/assets/8d4ad108-5cdc-4334-a7ad-7c139c8a40fb" />

# Results
The analog signal was successfully encoded and reconstructed using PCM and DM techniques in Python, verifying their working principles.

