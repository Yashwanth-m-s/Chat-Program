import java.io.*;
import java.net.*;
import java.util.*;

public class chatserver {
    private static final int PORT = 99999;
    private static Set<PrintWriter> clientWriters = new HashSet<>();

    public static void main(String[] args) {
        try (ServerSocket serverSocket = new ServerSocket(PORT)) {
            System.out.println("Chat Server is running on port " + PORT);

            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("New client connected: " + clientSocket);

                ClientHandler clientHandler = new ClientHandler(clientSocket);
                new Thread(clientHandler).start();
            }
        } catch (IOException e) {
            System.err.println("Error in the server: " + e.getMessage());
        }
    }

    public static void broadcastMessage(String message, PrintWriter sender) {
        for (PrintWriter writer : clientWriters) {
            if (writer != sender) {
                writer.println(message);
            }
        }
    }

    private static class ClientHandler implements Runnable {
        private Socket clientSocket;
        private PrintWriter writer;

        public ClientHandler(Socket socket) {
            this.clientSocket = socket;
        }

        @Override
        public void run() {
            try {
                BufferedReader reader = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                writer = new PrintWriter(clientSocket.getOutputStream(), true);

                synchronized (clientWriters) {
                    clientWriters.add(writer);
                }

                String incomingMessage;
                while ((incomingMessage = reader.readLine()) != null) {
                    System.out.println("Received message from client: " + incomingMessage);
                    broadcastMessage(incomingMessage, writer);
                }
            } catch (IOException e) {
                System.err.println("Error handling client: " + e.getMessage());
            } finally {
                if (writer != null) {
                    synchronized (clientWriters) {
                        clientWriters.remove(writer);
                    }
                }
                try {
                    clientSocket.close();
                } catch (IOException e) {
                    System.err.println("Error closing socket: " + e.getMessage());
                }
            }
        }
    }
}
