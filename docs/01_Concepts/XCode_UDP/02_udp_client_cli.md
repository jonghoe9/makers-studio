# XCode CLI | UDP Client (Broadcast)

```swift
//
//  main.swift
//  wift_udp_client_broadcast_cli
//
// 1초에 한번씩 브로드캐스트로 메세지 전송하는 프로그램
//

import Foundation
import Network

class UDPBroadcastClient {
    private var connection: NWConnection?
    private var timer: Timer?
    private let queue = DispatchQueue(label: "UDPBroadcastQueue")
    
    // 브로드캐스트 주소와 포트 설정
    private let broadcastAddress = NWEndpoint.Host("255.255.255.255")
    private let port = NWEndpoint.Port(rawValue: 8888)!
    
    func startBroadcasting() {
        let parameters = NWParameters.udp
        if let ipv4Options = parameters.defaultProtocolStack.internetProtocol as? NWProtocolIP.Options {
            ipv4Options.version = .v4
        }
        connection = NWConnection(host: broadcastAddress, port: port, using: parameters)
        connection?.stateUpdateHandler = { state in
            switch state {
            case .ready:
                print("브로드캐스트 준비 완료")
                self.setupTimer()
            case .failed(let error):
                print("연결 실패: \(error)")
            default:
                break
            }
        }
        connection?.start(queue: queue)
    }

    private func setupTimer() {
        DispatchQueue.main.async {
            self.timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { [weak self] _ in
                self?.sendPing()
            }
        }
    }

    private func sendPing() {
        let message = "idNINE Device Center Ping at \(Date().description)"
        let data = message.data(using: .utf8)
        
        connection?.send(content: data, completion: .contentProcessed({ error in
            if let error = error {
                print("전송 에러: \(error)")
            } else {
                print("메시지 브로드캐스트 성공: \(message)")
            }
        }))
    }

    func stop() {
        timer?.invalidate()
        connection?.cancel()
        print("브로드캐스트 중단")
    }
}

let client = UDPBroadcastClient()
client.startBroadcasting()

RunLoop.main.run()
```