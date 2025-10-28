import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String username = request.getParameter("username");
        String password = request.getParameter("password");

        // Hardcoded validation (can be extended to DB)
        if ("admin".equals(username) && "1234".equals(password)) {
            out.println("<h2>Welcome, " + username + "!</h2>");
        } else {
            out.println("<h3 style='color:red;'>Invalid username or password!</h3>");
            out.println("<a href='index.html'>Try Again</a>");
        }
        out.close();
    }
}


import java.io.*;
import java.sql.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class EmployeeServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String empid = request.getParameter("empid");
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/companydb", "root", "yourpassword");
            stmt = conn.createStatement();

            String query;
            if (empid != null && !empid.trim().isEmpty()) {
                query = "SELECT * FROM Employee WHERE EmpID = " + empid;
            } else {
                query = "SELECT * FROM Employee";
            }

            rs = stmt.executeQuery(query);
            out.println("<table border='1'><tr><th>ID</th><th>Name</th><th>Salary</th></tr>");
            boolean found = false;
            while (rs.next()) {
                found = true;
                out.println("<tr><td>" + rs.getInt("EmpID") + "</td><td>" +
                            rs.getString("Name") + "</td><td>" +
                            rs.getDouble("Salary") + "</td></tr>");
            }
            out.println("</table>");
            if (!found) out.println("<p>No record found.</p>");
            out.println("<br><a href='employee.html'>Back</a>");
        } catch (Exception e) {
            out.println("<p>Error: " + e.getMessage() + "</p>");
        } finally {
            try { if (rs != null) rs.close(); if (stmt != null) stmt.close(); if (conn != null) conn.close(); } catch (Exception ignored) {}
        }
    }
}


import java.io.*;
import java.sql.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class AttendanceServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String id = request.getParameter("studentid");
        String date = request.getParameter("date");
        String status = request.getParameter("status");

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/schooldb", "root", "yourpassword");

            PreparedStatement ps = conn.prepareStatement("INSERT INTO Attendance VALUES (?, ?, ?)");
            ps.setInt(1, Integer.parseInt(id));
            ps.setString(2, date);
            ps.setString(3, status);

            int i = ps.executeUpdate();
            if (i > 0)
                out.println("<h3>Attendance Recorded Successfully!</h3>");
            else
                out.println("<h3 style='color:red;'>Failed to Record Attendance.</h3>");
            
            out.println("<br><a href='attendance.jsp'>Go Back</a>");
            conn.close();
        } catch (Exception e) {
            out.println("<p>Error: " + e.getMessage() + "</p>");
        }
    }
}

