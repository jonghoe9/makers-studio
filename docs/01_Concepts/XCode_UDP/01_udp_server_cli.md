# XCode CLI | UDP Server

```swift
//
//  main.swift
//  swift_udp_server_cli
//

import Foundation
import Network

let port: NWEndpoint.Port = 50000
let parameters = NWParameters.udp
//parameters.requiredInterfaceType = .wifi
parameters.allowLocalEndpointReuse = true

do {
    let listener = try NWListener(using: parameters, on: port)
    listener.newConnectionHandler = { connection in
        connection.start(queue: .global())
        func receiveNextMessage() {
            connection.receiveMessage { (data, context, isComplete, error) in
                if let data = data,
                   let message = String(data: data, encoding: .utf8) {
                    if let localEndpoint = connection.currentPath?.localEndpoint {
                        print("Listen: \(localEndpoint)")
                    }
                    print("From: \(connection.endpoint)")
                    print("Message: \(message)")
                    print("--------------------------------")
                }
                if error == nil {
                    receiveNextMessage()
                } else {
                    connection.cancel()
                }
            }
        }
        receiveNextMessage()
    }
    
    listener.stateUpdateHandler = { state in
        switch state {
        case .ready:
            print("UDP Server ready on \(port)")
        case .failed(let error):
            print("Server Error: \(error)")
            exit(EXIT_FAILURE)
        default:
            break
        }
    }
    listener.start(queue: .main)
} catch {
    print("Server Strart Failed: \(error)")
    exit(1)
}

RunLoop.main.run()

```