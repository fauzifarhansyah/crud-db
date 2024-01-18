# crud-db
Untuk memenuhi tugas mata kuliah Pemograman Visual (Desktop) mengenai membuat aplikasi crud sederhana

#1. Persiapkan lingkungan pengembangan:
- Instal Java Development Kit (JDK) dan pastikan sudah terkonfigurasi dengan benar.
- Unduh dan instal NetBeans IDE.
- Unduh dan instal XAMPP, lalu nyalakan Apache dan MySQL di XAMPP Control Panel.

#2. Buat database di MySQL: 
- Buka XAMPP Control Panel dan klik tombol "Admin" di sebelah MySQL untuk membuka phpMyAdmin.
- Buat database baru dengan nama "crud_db" atau sesuai keinginan Anda.
  -- Membuat tabel "users"
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) NOT NULL,
  password VARCHAR(50) NOT NULL,
  email VARCHAR(100) NOT NULL
);

-- Membuat tabel "biodata"
CREATE TABLE biodata (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) NOT NULL,
  full_name VARCHAR(100) NOT NULL,
  address VARCHAR(200) NOT NULL,
  FOREIGN KEY (username) REFERENCES users(username)
);


#3. Buat proyek baru di NetBeans: 
- Buka NetBeans IDE.
- Pilih "File" -> "New Project".
- Pilih "Java" -> "Java Application" dan klik "Next".
- Beri nama proyek Anda dan klik "Finish".

#4. Tambahkan library JDBC: 
- Klik kanan pada proyek Anda di panel "Projects" di NetBeans.
- Pilih "Properties".
- Pilih "Libraries" di sebelah kiri.
- Klik tombol "Add Library".
- Pilih "MySQL JDBC Driver" dan klik "Add Library".

#5. Buat koneksi database: 
- Buat class baru dengan nama "DBConnection" dan tambahkan kode berikut:

'''
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
public class DBConnection {
    private static final String URL = "jdbc:mysql://localhost:3306/crud_db";
    private static final String USERNAME = "root";
    private static final String PASSWORD = "";
    public static Connection getConnection() {
        Connection connection = null;
        try {
            connection = DriverManager.getConnection(URL, USERNAME, PASSWORD);
            System.out.println("Connected to database!");
        } catch (SQLException e) {
            System.out.println("Failed to connect to database!");
            e.printStackTrace();
        }
        return connection;
    }
}
'''
 
#6. Buat class untuk registrasi:
- Buat class baru dengan nama "Registration" dan tambahkan kode berikut:

'''
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class Registration {
    public static void register(String username, String password, String email) {
        Connection connection = DBConnection.getConnection();
        try {
            PreparedStatement statement = connection.prepareStatement("INSERT INTO users (username, password, email) VALUES (?, ?, ?)");
            statement.setString(1, username);
            statement.setString(2, password);
            statement.setString(3, email);
            statement.executeUpdate();
            System.out.println("Registration successful!");
        } catch (SQLException e) {
            System.out.println("Registration failed!");
            e.printStackTrace();
        }
    }
}
'''
 
#7. Buat class untuk login:
- Buat class baru dengan nama "Login" dan tambahkan kode berikut:
 
'''
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class Login {
    public static boolean authenticate(String username, String password) {
        Connection connection = DBConnection.getConnection();
        try {
            PreparedStatement statement = connection.prepareStatement("SELECT * FROM users WHERE username = ? AND password = ?");
            statement.setString(1, username);
            statement.setString(2, password);
            ResultSet resultSet = statement.executeQuery();
            return resultSet.next();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }
}
'''
 
#8. Buat class untuk biodata:
- Buat class baru dengan nama "Biodata" dan tambahkan kode berikut:
 
'''
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class Biodata {
    public static void insert(String username, String fullName, String address) {
        Connection connection = DBConnection.getConnection();
        try {
            PreparedStatement statement = connection.prepareStatement("INSERT INTO biodata (username, full_name, address) VALUES (?, ?, ?)");
            statement.setString(1, username);
            statement.setString(2, fullName);
            statement.setString(3, address);
            statement.executeUpdate();
            System.out.println("Biodata inserted successfully!");
        } catch (SQLException e) {
            System.out.println("Failed to insert biodata!");
            e.printStackTrace();
        }
    }
    '''
    '''
    public static void view(String username) {
        Connection connection = DBConnection.getConnection();
        try {
            PreparedStatement statement = connection.prepareStatement("SELECT * FROM biodata WHERE username = ?");
            statement.setString(1, username);
            ResultSet resultSet = statement.executeQuery();
            if (resultSet.next()) {
                String fullName = resultSet.getString("full_name");
                String address = resultSet.getString("address");
                System.out.println("Full Name: " + fullName);
                System.out.println("Address: " + address);
            } else {
                System.out.println("Biodata not found!");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
''' 
 
9. Gunakan class-class yang sudah dibuat dalam class "Main":
- Buat class baru dengan nama "Main" dan tambahkan kode berikut:
 
'''
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Welcome to CRUD Application!");
        while (true) {
            System.out.println("1. Register");
            System.out.println("2. Login");
            System.out.println("3. Exit");
            System.out.print("Choose an option: ");
            int option = scanner.nextInt();
            scanner.nextLine();
            if (option == 1) {
                System.out.print("Enter username: ");
                String username = scanner.nextLine();
                System.out.print("Enter password: ");
                String password = scanner.nextLine();
                System.out.print("Enter email: ");
                String email = scanner.nextLine();
                Registration.register(username, password, email);
            } else if (option == 2) {
                System.out.print("Enter username: ");
                String username = scanner.nextLine();
                System.out.print("Enter password: ");
                String password = scanner.nextLine();
                boolean authenticated = Login.authenticate(username, password);
                if (authenticated) {
                    System.out.println("Login successful!");
                    System.out.println("1. Insert Biodata");
                    System.out.println("2. View Biodata");
                    System.out.print("Choose an option: ");
                    int subOption = scanner.nextInt();
                    scanner.nextLine();
                    if (subOption == 1) {
                        System.out.print("Enter full name: ");
                        String fullName = scanner.nextLine();
                        System.out.print("Enter address: ");
                        String address = scanner.nextLine();
                        Biodata.insert(username, fullName, address);
                    } else if (subOption == 2) {
                        Biodata.view(username);
                    }
                } else {
                    System.out.println("Login failed!");
                }
            } else if (option == 3) {
                System.out.println("Thanks you for using CRUD Application!");
                break;
                } else {
                System.out.println("Invalid option! Please choose again.");
                  }
                }
                scanner.close();
            }
          }
