# AltDaemon_12.x_Fix

I patched the `BL _objc_release` to `NOP` at address 0x13DE8 of AltDaemon binary, and it seems to stop crashing.

```
__text:0000000100013DC0 loc_100013DC0                           ; CODE XREF: _$s9AltDaemon20XPCConnectionHandlerC8listener_25shouldAcceptNewConnectionSbSo13NSXPCListenerC_So15NSXPCConnectionCtFTf4dnn_n+380↑j
__text:0000000100013DC0                 LDUR            X0, [X29,#var_B0]
__text:0000000100013DC4                 BL              _swift_bridgeObjectRelease
__text:0000000100013DC8                 MOV             X0, X25
__text:0000000100013DCC                 BL              _objc_release
__text:0000000100013DD0                 MOV             X0, X26
__text:0000000100013DD4                 BL              _swift_bridgeObjectRelease
__text:0000000100013DD8                 LDUR            X0, [X29,#var_60]
__text:0000000100013DDC                 CBZ             X0, loc_100013DE8
__text:0000000100013DE0                 BL              _objc_release
__text:0000000100013DE4                 LDUR            X0, [X29,#var_60]
__text:0000000100013DE8
__text:0000000100013DE8 loc_100013DE8                           ; CODE XREF: _$s9AltDaemon20XPCConnectionHandlerC8listener_25shouldAcceptNewConnectionSbSo13NSXPCListenerC_So15NSXPCConnectionCtFTf4dnn_n+3C0↑j
__text:0000000100013DE8                 NOP                     ; Keypatch modified this from:
__text:0000000100013DE8                                         ;   BL _objc_release
__text:0000000100013DEC                 LDUR            X0, [X29,#var_58]
__text:0000000100013DF0                 CBZ             X0, loc_100013DF8
__text:0000000100013DF4                 BL              _swift_unknownObjectRelease
__text:0000000100013DF8
__text:0000000100013DF8 loc_100013DF8                           ; CODE XREF: _$s9AltDaemon20XPCConnectionHandlerC8listener_25shouldAcceptNewConnectionSbSo13NSXPCListenerC_So15NSXPCConnectionCtFTf4dnn_n+3D4↑j
__text:0000000100013DF8                 LDR             X8, [X27,#8]
__text:0000000100013DFC                 MOV             X0, X23
__text:0000000100013E00                 MOV             X1, X21
__text:0000000100013E04                 BLR             X8
__text:0000000100013E08                 MOV             W20, #1
__text:0000000100013E0C                 B               loc_100013AF8
```

Fix crash on ios 12.x like something below:

```
Date: 10/7/20, 7:41 PM
Process: AltDaemon
Bundle id: (null)
Device: iPhone 6 Plus, iOS 12.4.8
 
Exception type: EXC_BREAKPOINT (SIGTRAP)
Exception subtype: (null)
Exception codes: 0x0000000000000000, 0x00000001becca8c4
Culprit: Unknown
 
Triggered by thread: 1
Thread name: Dispatch queue: com.apple.NSXPCListener.service.cy:io.altstore.altdaemon
Call stack:
0   CoreFoundation                	0x00000001becca8c4 0x1bec1e000 + 706756     	// _CFRelease
1   AltDaemon                     	0x000000010093fdec 0x10092c000 + 81388      	// specialized XPCConnectionHandler.listener(_:shouldAcceptNewConnection:)
2   AltDaemon                     	0x000000010093fdec 0x10092c000 + 81388      	// specialized XPCConnectionHandler.listener(_:shouldAcceptNewConnection:)
3   AltDaemon                     	0x000000010093f4f4 0x10092c000 + 79092      	// @objc XPCConnectionHandler.listener(_:shouldAcceptNewConnection:)
4   Foundation                    	0x00000001bf8bc924 0x1bf68a000 + 2304292    	// service_connection_handler_make_connection
5   libxpc.dylib                  	0x00000001be98dafc 0x1be982000 + 47868      	// _xpc_connection_call_event_handler
6   libxpc.dylib                  	0x00000001be98dff0 0x1be982000 + 49136      	// _xpc_connection_mach_event
7   libdispatch.dylib             	0x00000001be775894 0x1be715000 + 395412     	// _dispatch_client_callout4
8   libdispatch.dylib             	0x00000001be72d5c0 0x1be715000 + 99776      	// _dispatch_mach_msg_invoke$VARIANT$mp
9   libdispatch.dylib             	0x00000001be71e1f0 0x1be715000 + 37360      	// _dispatch_lane_serial_drain$VARIANT$mp
10  libdispatch.dylib             	0x00000001be72e1cc 0x1be715000 + 102860     	// _dispatch_mach_invoke$VARIANT$mp
11  libdispatch.dylib             	0x00000001be71e1f0 0x1be715000 + 37360      	// _dispatch_lane_serial_drain$VARIANT$mp
12  libdispatch.dylib             	0x00000001be71ee74 0x1be715000 + 40564      	// _dispatch_lane_invoke$VARIANT$mp
13  libdispatch.dylib             	0x00000001be7274ac 0x1be715000 + 74924      	// _dispatch_workloop_worker_thread
14  libsystem_pthread.dylib       	0x00000001be956114 0x1be94a000 + 49428      	// _pthread_wqthread
15  libsystem_pthread.dylib       	0x00000001be958cd4 0x1be94a000 + 60628      	// start_wqthread
 
Register values:
PC: 0x1becca8c4         LR: 0x10093fdec         CPSR: 0x60000000
x0: 0x100f3b2b0         x1: 0x1eba59b04         x2: 0xf03
x3: 0x6f                x4: 0x100f83450         x5: 0x4
x6: 0x0                 x7: 0x403               x8: 0x1f6079b58
x9: 0x1bedbabb2         x10: 0x10103db10        x11: 0x600000007
x12: 0x10103db50        x13: 0x1a1f832f731      x14: 0xe028
x15: 0x4000             x16: 0x1f832f730        x17: 0x1bec41a2c
x18: 0x0                x19: 0x102009a00        x20: 0x100f00290
x21: 0x100f3b2b8        x22: 0x100f0b830        x23: 0x16f55a540
x24: 0x100938fd0        x25: 0x601280           x26: 0x10180be00
x27: 0x1f5eace18        x28: 0x101042b30
 
Loaded images:
0: /usr/bin/AltDaemon
1: /usr/lib/substitute-inserter.dylib
2: /usr/lib/libsubstitute.dylib
3: /usr/lib/substitute-loader.dylib
4: /usr/lib/dyld
5: /usr/lib/libSystem.B.dylib
6: /usr/lib/libc++.1.dylib
7: /usr/lib/libc++abi.dylib
8: /usr/lib/libobjc.A.dylib
9: /usr/lib/system/libcache.dylib
10: /usr/lib/system/libcommonCrypto.dylib
11: /usr/lib/system/libcompiler_rt.dylib
12: /usr/lib/system/libcopyfile.dylib
13: /usr/lib/system/libcorecrypto.dylib
14: /usr/lib/system/libdispatch.dylib
15: /usr/lib/system/libdyld.dylib
16: /usr/lib/system/liblaunch.dylib
17: /usr/lib/system/libmacho.dylib
18: /usr/lib/system/libremovefile.dylib
19: /usr/lib/system/libsystem_asl.dylib
20: /usr/lib/system/libsystem_blocks.dylib
21: /usr/lib/system/libsystem_c.dylib
22: /usr/lib/system/libsystem_configuration.dylib
23: /usr/lib/system/libsystem_containermanager.dylib
24: /usr/lib/system/libsystem_coreservices.dylib
25: /usr/lib/system/libsystem_darwin.dylib
26: /usr/lib/system/libsystem_dnssd.dylib
27: /usr/lib/system/libsystem_info.dylib
28: /usr/lib/system/libsystem_kernel.dylib
29: /usr/lib/system/libsystem_m.dylib
30: /usr/lib/system/libsystem_malloc.dylib
31: /usr/lib/system/libsystem_networkextension.dylib
32: /usr/lib/system/libsystem_notify.dylib
33: /usr/lib/system/libsystem_platform.dylib
34: /usr/lib/system/libsystem_pthread.dylib
35: /usr/lib/system/libsystem_sandbox.dylib
36: /usr/lib/system/libsystem_symptoms.dylib
37: /usr/lib/system/libsystem_trace.dylib
38: /usr/lib/system/libunwind.dylib
39: /usr/lib/system/libxpc.dylib
40: /usr/lib/libicucore.A.dylib
41: /usr/lib/libz.1.dylib
42: /System/Library/Frameworks/CoreFoundation.framework/CoreFoundation
43: /usr/lib/libbsm.0.dylib
44: /usr/lib/libenergytrace.dylib
45: /System/Library/Frameworks/IOKit.framework/Versions/A/IOKit
46: /usr/lib/libxml2.2.dylib
47: /usr/lib/libbz2.1.0.dylib
48: /usr/lib/liblzma.5.dylib
49: /usr/lib/libsqlite3.dylib
50: /usr/lib/libMobileGestalt.dylib
51: /System/Library/Frameworks/CFNetwork.framework/CFNetwork
52: /System/Library/Frameworks/Foundation.framework/Foundation
53: /System/Library/Frameworks/Security.framework/Security
54: /System/Library/Frameworks/SystemConfiguration.framework/SystemConfiguration
55: /usr/lib/libCRFSuite.dylib
56: /usr/lib/libapple_nghttp2.dylib
57: /usr/lib/libarchive.2.dylib
58: /usr/lib/libcoretls.dylib
59: /usr/lib/libcoretls_cfhelpers.dylib
60: /usr/lib/liblangid.dylib
61: /usr/lib/libnetwork.dylib
62: /usr/lib/libpcap.A.dylib
63: /System/Library/Frameworks/IOSurface.framework/IOSurface
64: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libBLAS.dylib
65: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libLAPACK.dylib
66: /System/Library/Frameworks/Accelerate.framework/Frameworks/vImage.framework/vImage
67: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libSparseBLAS.dylib
68: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libvMisc.dylib
69: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libBNNS.dylib
70: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libLinearAlgebra.dylib
71: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libQuadrature.dylib
72: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libSparse.dylib
73: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libvDSP.dylib
74: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/vecLib
75: /System/Library/Frameworks/Accelerate.framework/Accelerate
76: /usr/lib/libcompression.dylib
77: /System/Library/Frameworks/CoreGraphics.framework/CoreGraphics
78: /System/Library/PrivateFrameworks/IOAccelerator.framework/IOAccelerator
79: /System/Library/Frameworks/OpenGLES.framework/libCoreFSCache.dylib
80: /System/Library/Frameworks/Metal.framework/Metal
81: /System/Library/PrivateFrameworks/GraphicsServices.framework/GraphicsServices
82: /System/Library/Frameworks/MobileCoreServices.framework/MobileCoreServices
83: /System/Library/PrivateFrameworks/IOSurfaceAccelerator.framework/IOSurfaceAccelerator
84: /System/Library/PrivateFrameworks/AppleJPEG.framework/AppleJPEG
85: /System/Library/Frameworks/ImageIO.framework/ImageIO
86: /System/Library/PrivateFrameworks/BaseBoard.framework/BaseBoard
87: /System/Library/PrivateFrameworks/AssertionServices.framework/AssertionServices
88: /System/Library/PrivateFrameworks/CorePhoneNumbers.framework/CorePhoneNumbers
89: /System/Library/PrivateFrameworks/AppSupport.framework/AppSupport
90: /System/Library/PrivateFrameworks/CrashReporterSupport.framework/CrashReporterSupport
91: /System/Library/PrivateFrameworks/AggregateDictionary.framework/AggregateDictionary
92: /usr/lib/libTelephonyUtilDynamic.dylib
93: /System/Library/PrivateFrameworks/ProtocolBuffer.framework/ProtocolBuffer
94: /System/Library/PrivateFrameworks/MobileKeyBag.framework/MobileKeyBag
95: /System/Library/PrivateFrameworks/BackBoardServices.framework/BackBoardServices
96: /System/Library/PrivateFrameworks/FrontBoardServices.framework/FrontBoardServices
97: /System/Library/PrivateFrameworks/SpringBoardServices.framework/SpringBoardServices
98: /System/Library/PrivateFrameworks/PowerLog.framework/PowerLog
99: /System/Library/PrivateFrameworks/CommonUtilities.framework/CommonUtilities
100: /usr/lib/liblockdown.dylib
101: /System/Library/Frameworks/CoreData.framework/CoreData
102: /System/Library/PrivateFrameworks/TCC.framework/TCC
103: /usr/lib/libcupolicy.dylib
104: /System/Library/Frameworks/CoreTelephony.framework/CoreTelephony
105: /System/Library/Frameworks/Accounts.framework/Accounts
106: /System/Library/PrivateFrameworks/AppleSauce.framework/AppleSauce
107: /System/Library/PrivateFrameworks/DataMigration.framework/DataMigration
108: /System/Library/PrivateFrameworks/Netrb.framework/Netrb
109: /System/Library/PrivateFrameworks/PersistentConnection.framework/PersistentConnection
110: /usr/lib/libmis.dylib
111: /System/Library/PrivateFrameworks/ManagedConfiguration.framework/ManagedConfiguration
112: /usr/lib/libReverseProxyDevice.dylib
113: /usr/lib/libamsupport.dylib
114: /System/Library/Frameworks/OpenGLES.framework/libCoreVMClient.dylib
115: /System/Library/Frameworks/OpenGLES.framework/libCVMSPluginSupport.dylib
116: /usr/lib/libutil.dylib
117: /System/Library/Frameworks/OpenGLES.framework/libGLImage.dylib
118: /System/Library/PrivateFrameworks/APFS.framework/APFS
119: /System/Library/PrivateFrameworks/MediaKit.framework/MediaKit
120: /usr/lib/updaters/libSERestoreInfo.dylib
121: /System/Library/PrivateFrameworks/DiskImages.framework/DiskImages
122: /System/Library/Frameworks/OpenGLES.framework/libGFXShared.dylib
123: /usr/lib/libauthinstall.dylib
124: /System/Library/PrivateFrameworks/IOMobileFramebuffer.framework/IOMobileFramebuffer
125: /System/Library/Frameworks/OpenGLES.framework/OpenGLES
126: /System/Library/PrivateFrameworks/ColorSync.framework/ColorSync
127: /System/Library/Frameworks/CoreVideo.framework/CoreVideo
128: /usr/lib/libCTGreenTeaLogger.dylib
129: /System/Library/Frameworks/CoreAudio.framework/CoreAudio
130: /System/Library/PrivateFrameworks/UserFS.framework/UserFS
131: /System/Library/Frameworks/CoreMedia.framework/CoreMedia
132: /usr/lib/libprotobuf-lite.dylib
133: /usr/lib/libprotobuf.dylib
134: /usr/lib/libAWDSupportFramework.dylib
135: /System/Library/PrivateFrameworks/WirelessDiagnostics.framework/WirelessDiagnostics
136: /System/Library/Frameworks/VideoToolbox.framework/VideoToolbox
137: /System/Library/PrivateFrameworks/FontServices.framework/libFontParser.dylib
138: /System/Library/PrivateFrameworks/FontServices.framework/FontServices
139: /System/Library/Frameworks/CoreText.framework/CoreText
140: /System/Library/PrivateFrameworks/IntlPreferences.framework/IntlPreferences
141: /System/Library/PrivateFrameworks/RTCReporting.framework/RTCReporting
142: /System/Library/PrivateFrameworks/CoreBrightness.framework/CoreBrightness
143: /usr/lib/libAudioStatistics.dylib
144: /System/Library/Frameworks/AudioToolbox.framework/AudioToolbox
145: /System/Library/Frameworks/QuartzCore.framework/QuartzCore
146: /System/Library/Frameworks/MediaAccessibility.framework/MediaAccessibility
147: /usr/lib/libiconv.2.dylib
148: /System/Library/PrivateFrameworks/NetworkStatistics.framework/NetworkStatistics
149: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSCore.framework/MPSCore
150: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSImage.framework/MPSImage
151: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSMatrix.framework/MPSMatrix
152: /System/Library/PrivateFrameworks/CoreAUC.framework/CoreAUC
153: /System/Library/Frameworks/MediaToolbox.framework/MediaToolbox
154: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSNeuralNetwork.framework/MPSNeuralNetwork
155: /System/Library/Frameworks/MetalPerformanceShaders.framework/MetalPerformanceShaders
156: /System/Library/PrivateFrameworks/FaceCore.framework/FaceCore
157: /System/Library/PrivateFrameworks/GraphVisualizer.framework/GraphVisualizer
158: /usr/lib/libFosl_dynamic.dylib
159: /System/Library/Frameworks/CoreImage.framework/CoreImage
160: /System/Library/Frameworks/CoreBluetooth.framework/CoreBluetooth
161: /System/Library/PrivateFrameworks/PlugInKit.framework/PlugInKit
162: /System/Library/PrivateFrameworks/CacheDelete.framework/CacheDelete
163: /System/Library/PrivateFrameworks/StreamingZip.framework/StreamingZip
164: /System/Library/PrivateFrameworks/CoreEmoji.framework/CoreEmoji
165: /System/Library/PrivateFrameworks/CoreLocationProtobuf.framework/CoreLocationProtobuf
166: /System/Library/PrivateFrameworks/SymptomDiagnosticReporter.framework/SymptomDiagnosticReporter
167: /System/Library/PrivateFrameworks/GeoServices.framework/GeoServices
168: /System/Library/PrivateFrameworks/MobileAsset.framework/MobileAsset
169: /System/Library/PrivateFrameworks/Lexicon.framework/Lexicon
170: /usr/lib/libcmph.dylib
171: /System/Library/PrivateFrameworks/LanguageModeling.framework/LanguageModeling
172: /System/Library/Frameworks/CoreLocation.framework/CoreLocation
173: /System/Library/PrivateFrameworks/PhoneNumbers.framework/PhoneNumbers
174: /usr/lib/libChineseTokenizer.dylib
175: /usr/lib/libmecab_em.dylib
176: /usr/lib/libThaiTokenizer.dylib
177: /usr/lib/libgermantok.dylib
178: /System/Library/PrivateFrameworks/CoreNLP.framework/CoreNLP
179: /System/Library/Frameworks/JavaScriptCore.framework/JavaScriptCore
180: /usr/lib/libheimdal-asn1.dylib
181: /usr/lib/libate.dylib
182: /System/Library/PrivateFrameworks/TextureIO.framework/TextureIO
183: /System/Library/PrivateFrameworks/CoreUI.framework/CoreUI
184: /System/Library/PrivateFrameworks/MobileIcons.framework/MobileIcons
185: /System/Library/PrivateFrameworks/TextInput.framework/TextInput
186: /System/Library/Frameworks/FileProvider.framework/FileProvider
187: /System/Library/PrivateFrameworks/ProofReader.framework/ProofReader
188: /usr/lib/libAccessibility.dylib
189: /System/Library/PrivateFrameworks/WebCore.framework/Frameworks/libwebrtc.dylib
190: /System/Library/PrivateFrameworks/WebCore.framework/WebCore
191: /System/Library/PrivateFrameworks/WebKitLegacy.framework/WebKitLegacy
192: /System/Library/Frameworks/UserNotifications.framework/UserNotifications
193: /System/Library/PrivateFrameworks/AppleIDAuthSupport.framework/AppleIDAuthSupport
194: /System/Library/PrivateFrameworks/AuthKit.framework/AuthKit
195: /System/Library/Frameworks/UIKit.framework/UIKit
196: /System/Library/PrivateFrameworks/DocumentManagerCore.framework/DocumentManagerCore
197: /System/Library/PrivateFrameworks/HangTracer.framework/HangTracer
198: /System/Library/PrivateFrameworks/PhysicsKit.framework/PhysicsKit
199: /System/Library/PrivateFrameworks/StudyLog.framework/StudyLog
200: /System/Library/PrivateFrameworks/UIFoundation.framework/UIFoundation
201: /usr/lib/libresolv.9.dylib
202: /usr/lib/libtidy.A.dylib
203: /System/Library/PrivateFrameworks/IMFoundation.framework/IMFoundation
204: /System/Library/PrivateFrameworks/DiagnosticLogCollection.framework/DiagnosticLogCollection
205: /System/Library/PrivateFrameworks/Marco.framework/Marco
206: /System/Library/PrivateFrameworks/MessageProtection.framework/MessageProtection
207: /System/Library/PrivateFrameworks/StoreServices.framework/StoreServices
208: /System/Library/PrivateFrameworks/Engram.framework/Engram
209: /System/Library/PrivateFrameworks/IDSFoundation.framework/IDSFoundation
210: /System/Library/PrivateFrameworks/CaptiveNetwork.framework/CaptiveNetwork
211: /System/Library/PrivateFrameworks/EAP8021X.framework/EAP8021X
212: /System/Library/PrivateFrameworks/MobileWiFi.framework/MobileWiFi
213: /System/Library/PrivateFrameworks/OAuth.framework/OAuth
214: /System/Library/PrivateFrameworks/CommonAuth.framework/CommonAuth
215: /System/Library/PrivateFrameworks/Heimdal.framework/Heimdal
216: /System/Library/Frameworks/GSS.framework/GSS
217: /System/Library/PrivateFrameworks/ApplePushService.framework/ApplePushService
218: /System/Library/PrivateFrameworks/AccountsDaemon.framework/AccountsDaemon
219: /System/Library/PrivateFrameworks/AppleIDSSOAuthentication.framework/AppleIDSSOAuthentication
220: /System/Library/PrivateFrameworks/AppleAccount.framework/AppleAccount
221: /System/Library/PrivateFrameworks/CoreUtils.framework/CoreUtils
222: /System/Library/PrivateFrameworks/IDS.framework/IDS
223: /System/Library/PrivateFrameworks/UserManagement.framework/UserManagement
224: /System/Library/PrivateFrameworks/Bom.framework/Bom
225: /System/Library/PrivateFrameworks/PrototypeTools.framework/PrototypeTools
226: /System/Library/PrivateFrameworks/CoreSymbolication.framework/CoreSymbolication
227: /System/Library/PrivateFrameworks/CoreTime.framework/CoreTime
228: /System/Library/PrivateFrameworks/MobileSystemServices.framework/MobileSystemServices
229: /System/Library/PrivateFrameworks/ConstantClasses.framework/ConstantClasses
230: /System/Library/PrivateFrameworks/MobileInstallation.framework/MobileInstallation
231: /System/Library/PrivateFrameworks/CoreFollowUp.framework/CoreFollowUp
232: /System/Library/PrivateFrameworks/SetupAssistantSupport.framework/SetupAssistantSupport
233: /System/Library/PrivateFrameworks/SetupAssistant.framework/SetupAssistant
234: /System/Library/PrivateFrameworks/LinguisticData.framework/LinguisticData
235: /System/Library/PrivateFrameworks/MobileDeviceLink.framework/MobileDeviceLink
236: /System/Library/PrivateFrameworks/MobileBackup.framework/MobileBackup
237: /System/Library/PrivateFrameworks/LoggingSupport.framework/LoggingSupport
238: /System/Library/PrivateFrameworks/kperf.framework/kperf
239: /System/Library/PrivateFrameworks/kperfdata.framework/kperfdata
240: /usr/lib/libdscsym.dylib
241: /System/Library/PrivateFrameworks/ktrace.framework/ktrace
242: /System/Library/PrivateFrameworks/DeviceIdentity.framework/DeviceIdentity
243: /System/Library/PrivateFrameworks/Rapport.framework/Rapport
244: /System/Library/PrivateFrameworks/SignpostSupport.framework/SignpostSupport
245: /usr/lib/libtailspin.dylib
246: /System/Library/PrivateFrameworks/FontServices.framework/libGSFontCache.dylib
247: /System/Library/PrivateFrameworks/InternationalSupport.framework/InternationalSupport
248: /System/Library/PrivateFrameworks/SignpostCollection.framework/SignpostCollection
249: /System/Library/PrivateFrameworks/XCTTargetBootstrap.framework/XCTTargetBootstrap
250: /System/Library/PrivateFrameworks/libEDR.framework/libEDR
251: /usr/lib/libcharset.1.dylib
252: /System/Library/Frameworks/CoreServices.framework/CoreServices
253: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSRayIntersector.framework/MPSRayIntersector
254: /System/Library/Frameworks/Network.framework/Network
255: /System/Library/PrivateFrameworks/ASEProcessing.framework/ASEProcessing
256: /System/Library/PrivateFrameworks/AXCoreUtilities.framework/AXCoreUtilities
257: /System/Library/PrivateFrameworks/AppleMediaServices.framework/AppleMediaServices
258: /System/Library/PrivateFrameworks/DocumentManager.framework/DocumentManager
259: /System/Library/PrivateFrameworks/IdleTimerServices.framework/IdleTimerServices
260: /System/Library/PrivateFrameworks/OTSVG.framework/OTSVG
261: /System/Library/PrivateFrameworks/ROCKit.framework/ROCKit
262: /System/Library/PrivateFrameworks/SampleAnalysis.framework/SampleAnalysis
263: /System/Library/PrivateFrameworks/UIKitCore.framework/UIKitCore
264: /System/Library/PrivateFrameworks/UIKitServices.framework/UIKitServices
265: /System/Library/PrivateFrameworks/URLFormatting.framework/URLFormatting
266: /usr/lib/swift/libswiftCore.dylib
267: /usr/lib/swift/libswiftCoreFoundation.dylib
268: /usr/lib/swift/libswiftCoreGraphics.dylib
269: /usr/lib/swift/libswiftCoreImage.dylib
270: /usr/lib/swift/libswiftDarwin.dylib
271: /usr/lib/swift/libswiftDispatch.dylib
272: /usr/lib/swift/libswiftFoundation.dylib
273: /usr/lib/swift/libswiftMetal.dylib
274: /usr/lib/swift/libswiftNetwork.dylib
275: /usr/lib/swift/libswiftObjectiveC.dylib
276: /usr/lib/swift/libswiftQuartzCore.dylib
277: /usr/lib/swift/libswiftUIKit.dylib
 
 
{"ProcessBundleID":"","ProcessName":"AltDaemon","Culprit":"Unknown"}
RAW Paste Data
Date: 10/7/20, 7:41 PM
Process: AltDaemon
Bundle id: (null)
Device: iPhone 6 Plus, iOS 12.4.8

Exception type: EXC_BREAKPOINT (SIGTRAP)
Exception subtype: (null)
Exception codes: 0x0000000000000000, 0x00000001becca8c4
Culprit: Unknown

Triggered by thread: 1
Thread name: Dispatch queue: com.apple.NSXPCListener.service.cy:io.altstore.altdaemon
Call stack:
0   CoreFoundation                	0x00000001becca8c4 0x1bec1e000 + 706756     	// _CFRelease
1   AltDaemon                     	0x000000010093fdec 0x10092c000 + 81388      	// specialized XPCConnectionHandler.listener(_:shouldAcceptNewConnection:)
2   AltDaemon                     	0x000000010093fdec 0x10092c000 + 81388      	// specialized XPCConnectionHandler.listener(_:shouldAcceptNewConnection:)
3   AltDaemon                     	0x000000010093f4f4 0x10092c000 + 79092      	// @objc XPCConnectionHandler.listener(_:shouldAcceptNewConnection:)
4   Foundation                    	0x00000001bf8bc924 0x1bf68a000 + 2304292    	// service_connection_handler_make_connection
5   libxpc.dylib                  	0x00000001be98dafc 0x1be982000 + 47868      	// _xpc_connection_call_event_handler
6   libxpc.dylib                  	0x00000001be98dff0 0x1be982000 + 49136      	// _xpc_connection_mach_event
7   libdispatch.dylib             	0x00000001be775894 0x1be715000 + 395412     	// _dispatch_client_callout4
8   libdispatch.dylib             	0x00000001be72d5c0 0x1be715000 + 99776      	// _dispatch_mach_msg_invoke$VARIANT$mp
9   libdispatch.dylib             	0x00000001be71e1f0 0x1be715000 + 37360      	// _dispatch_lane_serial_drain$VARIANT$mp
10  libdispatch.dylib             	0x00000001be72e1cc 0x1be715000 + 102860     	// _dispatch_mach_invoke$VARIANT$mp
11  libdispatch.dylib             	0x00000001be71e1f0 0x1be715000 + 37360      	// _dispatch_lane_serial_drain$VARIANT$mp
12  libdispatch.dylib             	0x00000001be71ee74 0x1be715000 + 40564      	// _dispatch_lane_invoke$VARIANT$mp
13  libdispatch.dylib             	0x00000001be7274ac 0x1be715000 + 74924      	// _dispatch_workloop_worker_thread
14  libsystem_pthread.dylib       	0x00000001be956114 0x1be94a000 + 49428      	// _pthread_wqthread
15  libsystem_pthread.dylib       	0x00000001be958cd4 0x1be94a000 + 60628      	// start_wqthread

Register values:
PC: 0x1becca8c4         LR: 0x10093fdec         CPSR: 0x60000000
x0: 0x100f3b2b0         x1: 0x1eba59b04         x2: 0xf03
x3: 0x6f                x4: 0x100f83450         x5: 0x4
x6: 0x0                 x7: 0x403               x8: 0x1f6079b58
x9: 0x1bedbabb2         x10: 0x10103db10        x11: 0x600000007
x12: 0x10103db50        x13: 0x1a1f832f731      x14: 0xe028
x15: 0x4000             x16: 0x1f832f730        x17: 0x1bec41a2c
x18: 0x0                x19: 0x102009a00        x20: 0x100f00290
x21: 0x100f3b2b8        x22: 0x100f0b830        x23: 0x16f55a540
x24: 0x100938fd0        x25: 0x601280           x26: 0x10180be00
x27: 0x1f5eace18        x28: 0x101042b30

Loaded images:
0: /usr/bin/AltDaemon
1: /usr/lib/substitute-inserter.dylib
2: /usr/lib/libsubstitute.dylib
3: /usr/lib/substitute-loader.dylib
4: /usr/lib/dyld
5: /usr/lib/libSystem.B.dylib
6: /usr/lib/libc++.1.dylib
7: /usr/lib/libc++abi.dylib
8: /usr/lib/libobjc.A.dylib
9: /usr/lib/system/libcache.dylib
10: /usr/lib/system/libcommonCrypto.dylib
11: /usr/lib/system/libcompiler_rt.dylib
12: /usr/lib/system/libcopyfile.dylib
13: /usr/lib/system/libcorecrypto.dylib
14: /usr/lib/system/libdispatch.dylib
15: /usr/lib/system/libdyld.dylib
16: /usr/lib/system/liblaunch.dylib
17: /usr/lib/system/libmacho.dylib
18: /usr/lib/system/libremovefile.dylib
19: /usr/lib/system/libsystem_asl.dylib
20: /usr/lib/system/libsystem_blocks.dylib
21: /usr/lib/system/libsystem_c.dylib
22: /usr/lib/system/libsystem_configuration.dylib
23: /usr/lib/system/libsystem_containermanager.dylib
24: /usr/lib/system/libsystem_coreservices.dylib
25: /usr/lib/system/libsystem_darwin.dylib
26: /usr/lib/system/libsystem_dnssd.dylib
27: /usr/lib/system/libsystem_info.dylib
28: /usr/lib/system/libsystem_kernel.dylib
29: /usr/lib/system/libsystem_m.dylib
30: /usr/lib/system/libsystem_malloc.dylib
31: /usr/lib/system/libsystem_networkextension.dylib
32: /usr/lib/system/libsystem_notify.dylib
33: /usr/lib/system/libsystem_platform.dylib
34: /usr/lib/system/libsystem_pthread.dylib
35: /usr/lib/system/libsystem_sandbox.dylib
36: /usr/lib/system/libsystem_symptoms.dylib
37: /usr/lib/system/libsystem_trace.dylib
38: /usr/lib/system/libunwind.dylib
39: /usr/lib/system/libxpc.dylib
40: /usr/lib/libicucore.A.dylib
41: /usr/lib/libz.1.dylib
42: /System/Library/Frameworks/CoreFoundation.framework/CoreFoundation
43: /usr/lib/libbsm.0.dylib
44: /usr/lib/libenergytrace.dylib
45: /System/Library/Frameworks/IOKit.framework/Versions/A/IOKit
46: /usr/lib/libxml2.2.dylib
47: /usr/lib/libbz2.1.0.dylib
48: /usr/lib/liblzma.5.dylib
49: /usr/lib/libsqlite3.dylib
50: /usr/lib/libMobileGestalt.dylib
51: /System/Library/Frameworks/CFNetwork.framework/CFNetwork
52: /System/Library/Frameworks/Foundation.framework/Foundation
53: /System/Library/Frameworks/Security.framework/Security
54: /System/Library/Frameworks/SystemConfiguration.framework/SystemConfiguration
55: /usr/lib/libCRFSuite.dylib
56: /usr/lib/libapple_nghttp2.dylib
57: /usr/lib/libarchive.2.dylib
58: /usr/lib/libcoretls.dylib
59: /usr/lib/libcoretls_cfhelpers.dylib
60: /usr/lib/liblangid.dylib
61: /usr/lib/libnetwork.dylib
62: /usr/lib/libpcap.A.dylib
63: /System/Library/Frameworks/IOSurface.framework/IOSurface
64: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libBLAS.dylib
65: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libLAPACK.dylib
66: /System/Library/Frameworks/Accelerate.framework/Frameworks/vImage.framework/vImage
67: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libSparseBLAS.dylib
68: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libvMisc.dylib
69: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libBNNS.dylib
70: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libLinearAlgebra.dylib
71: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libQuadrature.dylib
72: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libSparse.dylib
73: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/libvDSP.dylib
74: /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/vecLib
75: /System/Library/Frameworks/Accelerate.framework/Accelerate
76: /usr/lib/libcompression.dylib
77: /System/Library/Frameworks/CoreGraphics.framework/CoreGraphics
78: /System/Library/PrivateFrameworks/IOAccelerator.framework/IOAccelerator
79: /System/Library/Frameworks/OpenGLES.framework/libCoreFSCache.dylib
80: /System/Library/Frameworks/Metal.framework/Metal
81: /System/Library/PrivateFrameworks/GraphicsServices.framework/GraphicsServices
82: /System/Library/Frameworks/MobileCoreServices.framework/MobileCoreServices
83: /System/Library/PrivateFrameworks/IOSurfaceAccelerator.framework/IOSurfaceAccelerator
84: /System/Library/PrivateFrameworks/AppleJPEG.framework/AppleJPEG
85: /System/Library/Frameworks/ImageIO.framework/ImageIO
86: /System/Library/PrivateFrameworks/BaseBoard.framework/BaseBoard
87: /System/Library/PrivateFrameworks/AssertionServices.framework/AssertionServices
88: /System/Library/PrivateFrameworks/CorePhoneNumbers.framework/CorePhoneNumbers
89: /System/Library/PrivateFrameworks/AppSupport.framework/AppSupport
90: /System/Library/PrivateFrameworks/CrashReporterSupport.framework/CrashReporterSupport
91: /System/Library/PrivateFrameworks/AggregateDictionary.framework/AggregateDictionary
92: /usr/lib/libTelephonyUtilDynamic.dylib
93: /System/Library/PrivateFrameworks/ProtocolBuffer.framework/ProtocolBuffer
94: /System/Library/PrivateFrameworks/MobileKeyBag.framework/MobileKeyBag
95: /System/Library/PrivateFrameworks/BackBoardServices.framework/BackBoardServices
96: /System/Library/PrivateFrameworks/FrontBoardServices.framework/FrontBoardServices
97: /System/Library/PrivateFrameworks/SpringBoardServices.framework/SpringBoardServices
98: /System/Library/PrivateFrameworks/PowerLog.framework/PowerLog
99: /System/Library/PrivateFrameworks/CommonUtilities.framework/CommonUtilities
100: /usr/lib/liblockdown.dylib
101: /System/Library/Frameworks/CoreData.framework/CoreData
102: /System/Library/PrivateFrameworks/TCC.framework/TCC
103: /usr/lib/libcupolicy.dylib
104: /System/Library/Frameworks/CoreTelephony.framework/CoreTelephony
105: /System/Library/Frameworks/Accounts.framework/Accounts
106: /System/Library/PrivateFrameworks/AppleSauce.framework/AppleSauce
107: /System/Library/PrivateFrameworks/DataMigration.framework/DataMigration
108: /System/Library/PrivateFrameworks/Netrb.framework/Netrb
109: /System/Library/PrivateFrameworks/PersistentConnection.framework/PersistentConnection
110: /usr/lib/libmis.dylib
111: /System/Library/PrivateFrameworks/ManagedConfiguration.framework/ManagedConfiguration
112: /usr/lib/libReverseProxyDevice.dylib
113: /usr/lib/libamsupport.dylib
114: /System/Library/Frameworks/OpenGLES.framework/libCoreVMClient.dylib
115: /System/Library/Frameworks/OpenGLES.framework/libCVMSPluginSupport.dylib
116: /usr/lib/libutil.dylib
117: /System/Library/Frameworks/OpenGLES.framework/libGLImage.dylib
118: /System/Library/PrivateFrameworks/APFS.framework/APFS
119: /System/Library/PrivateFrameworks/MediaKit.framework/MediaKit
120: /usr/lib/updaters/libSERestoreInfo.dylib
121: /System/Library/PrivateFrameworks/DiskImages.framework/DiskImages
122: /System/Library/Frameworks/OpenGLES.framework/libGFXShared.dylib
123: /usr/lib/libauthinstall.dylib
124: /System/Library/PrivateFrameworks/IOMobileFramebuffer.framework/IOMobileFramebuffer
125: /System/Library/Frameworks/OpenGLES.framework/OpenGLES
126: /System/Library/PrivateFrameworks/ColorSync.framework/ColorSync
127: /System/Library/Frameworks/CoreVideo.framework/CoreVideo
128: /usr/lib/libCTGreenTeaLogger.dylib
129: /System/Library/Frameworks/CoreAudio.framework/CoreAudio
130: /System/Library/PrivateFrameworks/UserFS.framework/UserFS
131: /System/Library/Frameworks/CoreMedia.framework/CoreMedia
132: /usr/lib/libprotobuf-lite.dylib
133: /usr/lib/libprotobuf.dylib
134: /usr/lib/libAWDSupportFramework.dylib
135: /System/Library/PrivateFrameworks/WirelessDiagnostics.framework/WirelessDiagnostics
136: /System/Library/Frameworks/VideoToolbox.framework/VideoToolbox
137: /System/Library/PrivateFrameworks/FontServices.framework/libFontParser.dylib
138: /System/Library/PrivateFrameworks/FontServices.framework/FontServices
139: /System/Library/Frameworks/CoreText.framework/CoreText
140: /System/Library/PrivateFrameworks/IntlPreferences.framework/IntlPreferences
141: /System/Library/PrivateFrameworks/RTCReporting.framework/RTCReporting
142: /System/Library/PrivateFrameworks/CoreBrightness.framework/CoreBrightness
143: /usr/lib/libAudioStatistics.dylib
144: /System/Library/Frameworks/AudioToolbox.framework/AudioToolbox
145: /System/Library/Frameworks/QuartzCore.framework/QuartzCore
146: /System/Library/Frameworks/MediaAccessibility.framework/MediaAccessibility
147: /usr/lib/libiconv.2.dylib
148: /System/Library/PrivateFrameworks/NetworkStatistics.framework/NetworkStatistics
149: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSCore.framework/MPSCore
150: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSImage.framework/MPSImage
151: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSMatrix.framework/MPSMatrix
152: /System/Library/PrivateFrameworks/CoreAUC.framework/CoreAUC
153: /System/Library/Frameworks/MediaToolbox.framework/MediaToolbox
154: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSNeuralNetwork.framework/MPSNeuralNetwork
155: /System/Library/Frameworks/MetalPerformanceShaders.framework/MetalPerformanceShaders
156: /System/Library/PrivateFrameworks/FaceCore.framework/FaceCore
157: /System/Library/PrivateFrameworks/GraphVisualizer.framework/GraphVisualizer
158: /usr/lib/libFosl_dynamic.dylib
159: /System/Library/Frameworks/CoreImage.framework/CoreImage
160: /System/Library/Frameworks/CoreBluetooth.framework/CoreBluetooth
161: /System/Library/PrivateFrameworks/PlugInKit.framework/PlugInKit
162: /System/Library/PrivateFrameworks/CacheDelete.framework/CacheDelete
163: /System/Library/PrivateFrameworks/StreamingZip.framework/StreamingZip
164: /System/Library/PrivateFrameworks/CoreEmoji.framework/CoreEmoji
165: /System/Library/PrivateFrameworks/CoreLocationProtobuf.framework/CoreLocationProtobuf
166: /System/Library/PrivateFrameworks/SymptomDiagnosticReporter.framework/SymptomDiagnosticReporter
167: /System/Library/PrivateFrameworks/GeoServices.framework/GeoServices
168: /System/Library/PrivateFrameworks/MobileAsset.framework/MobileAsset
169: /System/Library/PrivateFrameworks/Lexicon.framework/Lexicon
170: /usr/lib/libcmph.dylib
171: /System/Library/PrivateFrameworks/LanguageModeling.framework/LanguageModeling
172: /System/Library/Frameworks/CoreLocation.framework/CoreLocation
173: /System/Library/PrivateFrameworks/PhoneNumbers.framework/PhoneNumbers
174: /usr/lib/libChineseTokenizer.dylib
175: /usr/lib/libmecab_em.dylib
176: /usr/lib/libThaiTokenizer.dylib
177: /usr/lib/libgermantok.dylib
178: /System/Library/PrivateFrameworks/CoreNLP.framework/CoreNLP
179: /System/Library/Frameworks/JavaScriptCore.framework/JavaScriptCore
180: /usr/lib/libheimdal-asn1.dylib
181: /usr/lib/libate.dylib
182: /System/Library/PrivateFrameworks/TextureIO.framework/TextureIO
183: /System/Library/PrivateFrameworks/CoreUI.framework/CoreUI
184: /System/Library/PrivateFrameworks/MobileIcons.framework/MobileIcons
185: /System/Library/PrivateFrameworks/TextInput.framework/TextInput
186: /System/Library/Frameworks/FileProvider.framework/FileProvider
187: /System/Library/PrivateFrameworks/ProofReader.framework/ProofReader
188: /usr/lib/libAccessibility.dylib
189: /System/Library/PrivateFrameworks/WebCore.framework/Frameworks/libwebrtc.dylib
190: /System/Library/PrivateFrameworks/WebCore.framework/WebCore
191: /System/Library/PrivateFrameworks/WebKitLegacy.framework/WebKitLegacy
192: /System/Library/Frameworks/UserNotifications.framework/UserNotifications
193: /System/Library/PrivateFrameworks/AppleIDAuthSupport.framework/AppleIDAuthSupport
194: /System/Library/PrivateFrameworks/AuthKit.framework/AuthKit
195: /System/Library/Frameworks/UIKit.framework/UIKit
196: /System/Library/PrivateFrameworks/DocumentManagerCore.framework/DocumentManagerCore
197: /System/Library/PrivateFrameworks/HangTracer.framework/HangTracer
198: /System/Library/PrivateFrameworks/PhysicsKit.framework/PhysicsKit
199: /System/Library/PrivateFrameworks/StudyLog.framework/StudyLog
200: /System/Library/PrivateFrameworks/UIFoundation.framework/UIFoundation
201: /usr/lib/libresolv.9.dylib
202: /usr/lib/libtidy.A.dylib
203: /System/Library/PrivateFrameworks/IMFoundation.framework/IMFoundation
204: /System/Library/PrivateFrameworks/DiagnosticLogCollection.framework/DiagnosticLogCollection
205: /System/Library/PrivateFrameworks/Marco.framework/Marco
206: /System/Library/PrivateFrameworks/MessageProtection.framework/MessageProtection
207: /System/Library/PrivateFrameworks/StoreServices.framework/StoreServices
208: /System/Library/PrivateFrameworks/Engram.framework/Engram
209: /System/Library/PrivateFrameworks/IDSFoundation.framework/IDSFoundation
210: /System/Library/PrivateFrameworks/CaptiveNetwork.framework/CaptiveNetwork
211: /System/Library/PrivateFrameworks/EAP8021X.framework/EAP8021X
212: /System/Library/PrivateFrameworks/MobileWiFi.framework/MobileWiFi
213: /System/Library/PrivateFrameworks/OAuth.framework/OAuth
214: /System/Library/PrivateFrameworks/CommonAuth.framework/CommonAuth
215: /System/Library/PrivateFrameworks/Heimdal.framework/Heimdal
216: /System/Library/Frameworks/GSS.framework/GSS
217: /System/Library/PrivateFrameworks/ApplePushService.framework/ApplePushService
218: /System/Library/PrivateFrameworks/AccountsDaemon.framework/AccountsDaemon
219: /System/Library/PrivateFrameworks/AppleIDSSOAuthentication.framework/AppleIDSSOAuthentication
220: /System/Library/PrivateFrameworks/AppleAccount.framework/AppleAccount
221: /System/Library/PrivateFrameworks/CoreUtils.framework/CoreUtils
222: /System/Library/PrivateFrameworks/IDS.framework/IDS
223: /System/Library/PrivateFrameworks/UserManagement.framework/UserManagement
224: /System/Library/PrivateFrameworks/Bom.framework/Bom
225: /System/Library/PrivateFrameworks/PrototypeTools.framework/PrototypeTools
226: /System/Library/PrivateFrameworks/CoreSymbolication.framework/CoreSymbolication
227: /System/Library/PrivateFrameworks/CoreTime.framework/CoreTime
228: /System/Library/PrivateFrameworks/MobileSystemServices.framework/MobileSystemServices
229: /System/Library/PrivateFrameworks/ConstantClasses.framework/ConstantClasses
230: /System/Library/PrivateFrameworks/MobileInstallation.framework/MobileInstallation
231: /System/Library/PrivateFrameworks/CoreFollowUp.framework/CoreFollowUp
232: /System/Library/PrivateFrameworks/SetupAssistantSupport.framework/SetupAssistantSupport
233: /System/Library/PrivateFrameworks/SetupAssistant.framework/SetupAssistant
234: /System/Library/PrivateFrameworks/LinguisticData.framework/LinguisticData
235: /System/Library/PrivateFrameworks/MobileDeviceLink.framework/MobileDeviceLink
236: /System/Library/PrivateFrameworks/MobileBackup.framework/MobileBackup
237: /System/Library/PrivateFrameworks/LoggingSupport.framework/LoggingSupport
238: /System/Library/PrivateFrameworks/kperf.framework/kperf
239: /System/Library/PrivateFrameworks/kperfdata.framework/kperfdata
240: /usr/lib/libdscsym.dylib
241: /System/Library/PrivateFrameworks/ktrace.framework/ktrace
242: /System/Library/PrivateFrameworks/DeviceIdentity.framework/DeviceIdentity
243: /System/Library/PrivateFrameworks/Rapport.framework/Rapport
244: /System/Library/PrivateFrameworks/SignpostSupport.framework/SignpostSupport
245: /usr/lib/libtailspin.dylib
246: /System/Library/PrivateFrameworks/FontServices.framework/libGSFontCache.dylib
247: /System/Library/PrivateFrameworks/InternationalSupport.framework/InternationalSupport
248: /System/Library/PrivateFrameworks/SignpostCollection.framework/SignpostCollection
249: /System/Library/PrivateFrameworks/XCTTargetBootstrap.framework/XCTTargetBootstrap
250: /System/Library/PrivateFrameworks/libEDR.framework/libEDR
251: /usr/lib/libcharset.1.dylib
252: /System/Library/Frameworks/CoreServices.framework/CoreServices
253: /System/Library/Frameworks/MetalPerformanceShaders.framework/Frameworks/MPSRayIntersector.framework/MPSRayIntersector
254: /System/Library/Frameworks/Network.framework/Network
255: /System/Library/PrivateFrameworks/ASEProcessing.framework/ASEProcessing
256: /System/Library/PrivateFrameworks/AXCoreUtilities.framework/AXCoreUtilities
257: /System/Library/PrivateFrameworks/AppleMediaServices.framework/AppleMediaServices
258: /System/Library/PrivateFrameworks/DocumentManager.framework/DocumentManager
259: /System/Library/PrivateFrameworks/IdleTimerServices.framework/IdleTimerServices
260: /System/Library/PrivateFrameworks/OTSVG.framework/OTSVG
261: /System/Library/PrivateFrameworks/ROCKit.framework/ROCKit
262: /System/Library/PrivateFrameworks/SampleAnalysis.framework/SampleAnalysis
263: /System/Library/PrivateFrameworks/UIKitCore.framework/UIKitCore
264: /System/Library/PrivateFrameworks/UIKitServices.framework/UIKitServices
265: /System/Library/PrivateFrameworks/URLFormatting.framework/URLFormatting
266: /usr/lib/swift/libswiftCore.dylib
267: /usr/lib/swift/libswiftCoreFoundation.dylib
268: /usr/lib/swift/libswiftCoreGraphics.dylib
269: /usr/lib/swift/libswiftCoreImage.dylib
270: /usr/lib/swift/libswiftDarwin.dylib
271: /usr/lib/swift/libswiftDispatch.dylib
272: /usr/lib/swift/libswiftFoundation.dylib
273: /usr/lib/swift/libswiftMetal.dylib
274: /usr/lib/swift/libswiftNetwork.dylib
275: /usr/lib/swift/libswiftObjectiveC.dylib
276: /usr/lib/swift/libswiftQuartzCore.dylib
277: /usr/lib/swift/libswiftUIKit.dylib


{"ProcessBundleID":"","ProcessName":"AltDaemon","Culprit":"Unknown"}
Public Pastes
SMMR-119: Message
C++ | 34 min ago | 0.97 KB
Paste Ping
C | 42 min ago | 0.02 KB
modoverrides.lua
Lua | 1 hour ago | 11.56 KB
1ST EXERCISE AI
Python | 1 hour ago | 0.58 KB
soybean straw ls19
Lua | 3 hours ago | 1.92 KB
2021-07-28_stats.json
JSON | 3 hours ago | 18.89 KB
Paste Ping
C | 3 hours ago | 0.02 KB
lsblk without lsblk?
Python | 3 hours ago | 1.29 KB

```

