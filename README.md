import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

enum Level {
    DEBUG, WARNING, ERROR
}

interface Logger {
    void log(Level level, String message);
}

class LoggerConsole implements Logger {
    @Override
    public void log(Level level, String message) {
        String color;
        switch (level) {
            case DEBUG:
                color = "\u001B[32m"; // Verde
                break;
            case WARNING:
                color = "\u001B[33m"; // Amarelo
                break;
            case ERROR:
                color = "\u001B[31m"; // Vermelho
                break;
            default:
                color = "\u001B[0m"; // Reset
        }
        System.out.println(color + LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")) + " [" + level + "] " + message + "\u001B[0m");
    }
}

// Classe LoggerFile que implementa Logger
class LoggerFile implements Logger {
    private String filename;

    public LoggerFile(String filename) {
        this.filename = filename;
    }

    @Override
    public void log(Level level, String message) {
        try (PrintWriter out = new PrintWriter(new FileWriter(filename, true))) {
            out.println(LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")) + " [" + level + "] " + message);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

// Classe LoggerFactory para criar instâncias de Logger
class LoggerFactory {
    public static Logger createConsoleLogger() {
        return new LoggerConsole();
    }

    public static Logger createFileLogger(String filename) {
        return new LoggerFile(filename);
    }
}

public class Main {
    public static void main(String[] args) {
        Logger consoleLogger = LoggerFactory.createConsoleLogger();
        Logger fileLogger = LoggerFactory.createFileLogger("bruniville.log");

        consoleLogger.log(Level.DEBUG, "This is a debug message");
        consoleLogger.log(Level.WARNING, "This is a warning message");
        consoleLogger.log(Level.ERROR, "This is an error message");

        fileLogger.log(Level.DEBUG, "This is a debug message");
        fileLogger.log(Level.WARNING, "This is a warning message");
        fileLogger.log(Level.ERROR, "This is an error message");
    }
}
