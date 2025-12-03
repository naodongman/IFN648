Lightweight Cipher Benchmark
Ascon · PRESENT · Simon · Speck · AES-256

This project benchmarks five symmetric encryption algorithms across two hardware platforms—
a Windows 11 desktop (Intel i7) and a Raspberry Pi 3 Model A+—to evaluate:

Encryption speed

Memory usage

Code size (.text segment)

Lightweight suitability for IoT devices

Algorithms included:

Ascon AEAD (NIST LWC winner)

PRESENT

Simon

Speck

AES-256 (ECB, Tiny-AES-C)

📁 Project Structure
CryptoBenchmark/
├─ ascon-c/
│   └─ crypto_aead/...
├─ present-master/
│   ├─ include/
│   └─ src/
├─ Simon_Speck_Ciphers-master/
│   └─ C/
├─ tiny-AES-c-master/
│   └─ aes.c, aes.h
├─ wrapper.c
├─ main.cpp
└─ README.md


All algorithms are extracted from the original GitHub repositories and placed into a unified structure for compilation and benchmarking.

🔧 Build Instructions
1. Windows 11 (Visual Studio 2022)

Create a C++ Console Application.

Add the .c/.h files from:

ascon-c/.../crypto_aead

present-master/src & present-master/include

Simon_Speck_Ciphers-master/C

tiny-AES-c-master

Add include paths in project settings.

Set configuration:

Release x64
Optimization: /O2


Build & Run.

2. Raspberry Pi 3 Model A+

Install build tools:

sudo apt update
sudo apt install build-essential


Compile all cipher sources:

gcc -std=c11 -O2 -c \
  ascon-c/crypto_aead/asconaead128/opt64/aead.c \
  ascon-c/crypto_aead/asconaead128/opt64/permutations.c \
  present-master/src/present.c \
  Simon_Speck_Ciphers-master/C/simon.c \
  Simon_Speck_Ciphers-master/C/speck.c \
  tiny-AES-c-master/aes.c \
  wrapper.c


Link benchmark driver:

g++ -std=c++17 -O2 \
  main.cpp aead.o permutations.o present.o simon.o speck.o aes.o wrapper.o \
  -o CryptoBenchmark


Run:

./CryptoBenchmark

⚙ Benchmark Methodology
Test Input

Plaintext: 16 bytes of zeros (uint8_t pt[16] = {0})

Key: Zero-initialized key of appropriate length

Unified wrappers:
Each algorithm uses a wrapper function:

void wrap_xxx(const uint8_t* pt, uint8_t* ct, const uint8_t* key);

Speed Measurement

Timer: std::chrono::high_resolution_clock

Each algorithm runs RUNS times:

avg_latency = total_time / RUNS


Output: nanoseconds (ns)

Memory Usage

Windows: GetProcessMemoryInfo

Linux: read /proc/self/status → VmRSS

Code Size

Extracted from linker .map files via the .text segment size of each object file.

📊 Example Results
Windows 11 (Intel i7-13700KF)
Ascon   avg: 1351 ns
PRESENT avg: 3434 ns
Simon   avg: 523 ns
Speck   avg: 360 ns
AES256  avg: 1304 ns

Raspberry Pi 3 Model A+
Ascon   avg: 551 ns
PRESENT avg: 7157 ns
Simon   avg: 2571 ns
Speck   avg: 1268 ns
AES256  avg: 3373 ns

🔐 Security Summary (from literature)

Ascon — No practical attacks on full-round Ascon-128; strong AEAD security.

PRESENT — Best-known 31-round attack complexity ≈ 2⁶² (not practical).

Simon — Full-round variants remain secure; only reduced rounds affected.

Speck — Strong ARX-based resistance; no practical attacks on full rounds.

AES-256 — Most mature; no practical attacks on the full 14-round AES-256.

📝 Conclusion

Speck is the fastest on the x86-64 desktop platform.

Ascon is the fastest on ARM (Raspberry Pi) and provides strong AEAD security.

PRESENT is extremely lightweight but very slow in software.

Simon provides balanced performance with small code size.

AES-256 has the strongest security and hardware-acceleration potential.

This unified benchmarking framework enables clear performance comparisons across constrained and unconstrained environments.
