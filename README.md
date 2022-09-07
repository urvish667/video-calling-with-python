# Video calling with Python OpenCV
![download](https://user-images.githubusercontent.com/43312731/188880379-2ead17da-d3be-4426-82d7-d98f93938d0e.png)
## Hola
In this code, we will use socket programming and OpenCV for creating video chat app.
###Server side code
```
#Server
import socket
import cv2
import pickle
import struct
import imutils

server_socket = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
host_name = socket.gethostname() 
host_ip = socket.gethostbyname(host_name)
print("HOST IP:",host_ip)
port=9999 
socket_address = (host_ip,port)

server_socket.bind(socket_address)
server_socket.listen(5)
print("Listening at:",socket_address)

while True:
    client_socket,addr=server_socket.accept() 
    print('connecting from:', addr)
    if client_socket:
        vid=cv2.VideoCapture(0)

        while(vid.isOpened()):
            img, frame = vid.read()
            a = pickle.dumps(frame)
            message = struct.pack("Q", len(a))+a 
            client_socket.sendall(message)

            cv2.imshow("Video Transmitting", frame) 
            key=cv2.waitKey(1) & 0xFF
            if key== ord('q'):
                client_socket.close()

cv2.destroyAllWindows()
```
A server has a bind() method which binds it to a specific ip and port so that it can listen to incoming requests on that ip and port. A server has a listen() method which puts the server into listen mode. This allows the server to listen to incoming connections. And last a server has an accept() and close() method. The accept method initiates a connection with the client and the close method closes the connection with the client

###Client side
```
#Client
import socket
import cv2
import pickle
import struct
import imutils

client_socket = socket.socket(socket.AF_INET,socket.SOCK_STREAM) 
host_ip='169.254.122.39' #server ip
port=9999
client_socket.connect((host_ip,port)) 
data=b""
payload_size= struct.calcsize("Q")
while True:
    while len(data) < payload_size: 
        packet=client_socket.recv(4*1024)
        if not packet: break
        data+=packet

    packet_msg_size=data[:payload_size] 
    data = data[payload_size:] 
    msg_size= struct.unpack("Q", packet_msg_size)[0]

    while len(data) < msg_size:
        data += client_socket.recv(4*1024)
    frame_data = data[:msg_size] 
    data = data[msg_size:] 
    frame = pickle.loads(frame_data)
    cv2.imshow("Receiving video", frame) 
    key = cv2.waitKey(1) & 0xFF
    if key== ord("q"):
        break
        
client_socket.close()
cv2.destroyAllWindows()
```
Let us write a very simple client program which opens a connection to a given port 9999 and given host. This is very simple to create a socket client using Python’s socket module function.

The socket.connect(hosname, port ) opens a TCP connection to hostname on the port. Once you have a socket open, you can read from it like any IO object. When done, remember to close it, as you would close a file.

### Thanks you
