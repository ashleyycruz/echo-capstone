# ECHO — Enhanced Collaboration for Human-Robot Operations

**VCU College of Engineering · Capstone CS 25-347**

ECHO improves how industrial robots sense people and respond to them. The robot changes its movement, position and speed in real time based on where a person is, which way they're facing and what they're doing. It uses live location, velocity and trajectory tracking so the robot reacts to people safely and works with them efficiently.

The demo task is turn-based: a person and an ABB GoFa robot take turns building a tower of alternating red and blue blocks.
- **Vision** finds and classifies blocks in real time.
- **Task planning** orders the moves so the robot avoids unnecessary motion.
- **Visual and audio cues** plus proximity sensing signal whose turn it is and keep the work zone safe.
- **Safety and interface:** an emergency stop, plus a simple interface that gives the operator live feedback.

📄 **Read more:** [Project poster](docs/CS-25-347-Poster.pdf) · [Documentation report](docs/Documentation-Report.pdf)

---

## Repository layout

| Path | What's in it | Status |
|---|---|---|
| [`docs/`](docs) | Final poster and documentation report | ✅ |
| [`docs/deliverables/`](docs/deliverables) | Project proposal and team contracts | ✅ |
| [`src/magna-unity/`](src/magna-unity) | Main Unity project (`Magna`) with Nuitrack human detection, task sequences and the gripper model. Unity **2021.3.24f1**; has its own [setup README](src/magna-unity/README.md) | ✅ |
| [`src/abb/`](src/abb) | RAPID module `EGMCommunication.mod` that runs on the ABB controller | ✅ |
| [`src/robot-control/`](src/robot-control) | Standalone Unity project (`RobotControll`) for driving the GoFa over EGM/UDP. Unity **6000.0.23f1** | ✅ |
| [`src/mobile-control/`](src/mobile-control) | Mobile control app (`MobileControll`) | ⏳ still in Google Drive |
| [`status-reports/`](status-reports) | Project plan and weekly status reports (Fall weeks 1–12, Spring weeks 1–5) | ✅ |

## How it fits together

```
 Nuitrack (human tracking)
          │
          ▼
   Unity application  ──── UDP / EGM (port 6510–6511) ────▶  ABB GoFa controller (RAPID)
   (src/robot-control,                                         │
    src/magna-unity)   ◀──────── joint feedback ───────────────┘
          │
          └── TCP ──▶ Raspberry Pi (gripper / peripherals)
```

### Key scripts in `src/robot-control/Assets`

| Script | Role |
|---|---|
| `Scripts/EgmCommunication.cs` | Talks to the robot over ABB Externally Guided Motion (EGM), UDP port 6510 |
| `Scripts/UDPCOMM.cs` | Second EGM channel (port 6511) with a configurable robot IP |
| `Scripts/Egm.cs` | Generated Protobuf classes for EGM messages |
| `Scripts/UpdatedMove.cs` | Robot motion logic |
| `Scripts/SendDataToRaspberryPi.cs` | Sends commands to the Raspberry Pi over TCP |
| `UdpServerController.cs` | UDP server for request/release-control messages between clients |
| `JointDataSender.cs` | Broadcasts the robot's joint data over UDP |
| `TCPReciver.cs` | Listens for incoming data (UDP port 5005, despite the file name) |

Scenes: `Assets/Scenes/Gofa Control.unity`, `Assets/Scenes/RobotControll.unity`.
Unity version: **6000.0.23f1**.

---

## 🛠️ Setup

### 1. RobotStudio
1. **Connect to the Magnaforma Wi-Fi router.** It links your PC, the robot and the gripper.
2. **Find the IP addresses** of your PC, the ABB robot and the gripper (Wireshark or a similar tool works).
3. **Open RobotStudio** and connect to the controller.
4. **Create a UDP unicast device** in the RobotStudio configuration. Use your PC's IP address and give it a unique name (e.g. `MyPC_UDP`).
5. **Make sure the RAPID code references your device name.**
   > ⚠️ If the RAPID code doesn't reference your device, communication will fail.
6. Close RobotStudio.

📚 Reference: ABB *Externally Guided Motion (EGM)* application manual.

### 2. Unity
1. Open the main Unity project (`src/magna-unity`) in Unity **2021.3.24f1**, or the standalone `src/robot-control` project in Unity 6000.0.23f1. Unity rebuilds the `Library/` folder on first open, so that first open is slow.
2. The robot must be in **Auto** mode before you run the Unity app.

### 3. Nuitrack human detection
1. The Nuitrack SDK is embedded in the Unity project.
2. Launch the Nuitrack app and enter the license key (you may need to buy a new one).
3. Run the Unity project. Human detection should now work.

## ⚠️ Troubleshooting

- **Network problems:** make sure the PC, robot and gripper are all on the Magnaforma router (usually on the table next to the robot).
- **Wrong IP addresses in Unity:** some scripts hardcode IPs, e.g. `UDPCOMM.cs` (`192.168.0.4`) and `SendDataToRaspberryPi.cs` (`192.168.0.50`). Update them to match your network.
- **Robot locks up:** stop the Unity program, then reset the robot with the teach pendant or by jogging it.
- **General:** Unity errors show up in the Console. This project has many moving parts, so debug one layer at a time.

---

## Project team

| Name | Role |
|---|---|
| Shawn Brixey | Faculty Advisor, VCU College of Engineering |
| Tamer Nadeem | Faculty Advisor, VCU College of Engineering |
| Miles Popiela | Mentor, VCU College of Engineering |
| Ian Richards | Student, Computer Science |
| Ekta Shethna | Student, Computer Science |
| Gianni Bautista | Student, Computer Science |
| Samuel Sarzaba | Student, Computer Science |

## History

The `src/robot-control` code was originally kept in its own repository (`RobotControll`). It was imported here as a snapshot without that git history.
