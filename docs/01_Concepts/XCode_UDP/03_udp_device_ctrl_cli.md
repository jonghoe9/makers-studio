# XCode CLI | Device Center

```swift
//
//  main.swift
//  swift_udp_ctrl_center
//

import Foundation
import Network

// MARK: - [UDP Server, Listen]
func startUDPServer(port: NWEndpoint.Port) {
    let listener = try! NWListener(using: .udp, on: port)
    listener.newConnectionHandler = { conn in
        conn.start(queue: .global())
        func receive() {
            conn.receiveMessage { data, _, _, _ in
                if let data = data, let msg = String(data: data, encoding: .utf8) {
                    print("Received: \(msg)")
                }
                receive()
            }
        }
        receive()
    }
    listener.start(queue: .main)
    print("UDP Server started on port \(port)")
}

// MARK: - [UDP Client, Brocast Send]
class UDPBroadcastClient {
    private var connection: NWConnection?
    private let timer = DispatchSource.makeTimerSource(queue: .main)
    private let targetPort: NWEndpoint.Port
    
    private let dateFormatter: DateFormatter = {
        let df = DateFormatter()
        df.dateFormat = "yyyy.MM.dd HH:mm:ss 'KST'"
        df.timeZone = TimeZone(secondsFromGMT: 9 * 3600)
        return df
    }()

    init(port: UInt16) {
        self.targetPort = NWEndpoint.Port(rawValue: port)!
    }

    func start() {
        connection = NWConnection(host: "255.255.255.255", port: targetPort, using: .udp)
        connection?.start(queue: .main)
        
        timer.schedule(deadline: .now(), repeating: 1.0)
        timer.setEventHandler { [weak self] in
            guard let self = self else { return }
            let dateString = self.dateFormatter.string(from: Date())
            let message = "id9dc:, idNINE Device Center Ping at \(dateString)"
            
            self.connection?.send(content: message.data(using: .utf8), completion: .contentProcessed { error in
                if let error = error { print("Send Error: \(error)") }
            })
        }
        timer.resume()
        print("Broadcasting to port \(targetPort) ...")
    }
}

// MARK: - [실행부]
startUDPServer(port: 50000)

let client = UDPBroadcastClient(port: 54321)
client.start()

RunLoop.main.run()

```