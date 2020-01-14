# JSF-MySQLJDBC01
JSF Database Example – MySQL JDBC
guia do projeto:
https://www.journaldev.com/7068/jsf-database-example-mysql-jdbc

Welcome to JSF Database example. We will use JSF with MySQL database and use JDBC for our example. Java Server Faces can be connected to database to perform operations on the data using JDBC API.

Table of Contents
[
hide
]

1 JSF Database Example
1.1 Model Classes
JSF Database Example
Lets consider an example of pulling the data from a Car table residing in the database and display them using JDBC.

First, create the car table in mysql database as shown below.
Create a new database cardb as


CREATE DATABASE cardb;
Use the created database cardb to create table as


USE cardb;
Create the table car as shown below


CREATE TABLE Car(
	car_id INTEGER NOT NULL AUTO_INCREMENT,
	cname VARCHAR(60) NOT NULL,
	color VARCHAR(60),
	speed INTEGER,
	Manufactured_Country VARCHAR(100),
	PRIMARY KEY(car_id));
Now we shall insert some values into the Car table as


INSERT INTO Car VALUES(1,'Zen','Grey',45.34,'India');
INSERT INTO Car VALUES(2,'Volkswagen','Black',49.64,'Germany');
INSERT INTO Car VALUES(3,'Polo','White',52.33,'Japan');
INSERT INTO Car VALUES(4,'Audi','Blue',55.98,'Germany');
INSERT INTO Car VALUES(5,'Innova','Maroon',39.97,'France');
INSERT INTO Car VALUES(6,'FiatPalio','Silver',35.45,'Italy');
INSERT INTO Car VALUES(7,'Qualis','Red',23.35,'Paris');
Model Classes
Now Create the plain old java object class Car.java with the fields and getter and setter methods as shown below.


package com.journaldev.jsf.beans;

public class Car {

	private Integer cid;
	private String cname;
	private String color;
	private Integer speed;
	private String mfdctry;

	public Car() {
	}

	public Car(Integer cid, String cname, String color, Integer speed,
			String mfdctry) {
		this.cid = cid;
		this.cname = cname;
		this.color = color;
		this.speed = speed;
		this.mfdctry = mfdctry;
	}

	public Integer getCid() {
		return cid;
	}

	public void setCid(Integer cid) {
		this.cid = cid;
	}

	public String getCname() {
		return cname;
	}

	public void setCname(String cname) {
		this.cname = cname;
	}

	public String getColor() {
		return color;
	}

	public void setColor(String color) {
		this.color = color;
	}

	public Integer getSpeed() {
		return speed;
	}

	public void setSpeed(Integer speed) {
		this.speed = speed;
	}

	public String getMfdctry() {
		return mfdctry;
	}

	public void setMfdctry(String mfdctry) {
		this.mfdctry = mfdctry;
	}

}
Create a managed bean CarBean.java using which we will establish a connection to the database via Class.forName method to execute a query to pull the data from the car table. Note that this is very inefficient way to create and manage database connections. You should use JNDI Connection with Datasource or create a utility class for this. However for simplicity, I am not having those extra classes in my project.


package com.journaldev.jsf.beans;

import java.io.Serializable;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;
import javax.faces.bean.ManagedBean;
import javax.faces.bean.SessionScoped;

@ManagedBean
@SessionScoped
public class CarBean implements Serializable {

	private static final long serialVersionUID = 6081417964063918994L;

	public List<Car> getCars() throws ClassNotFoundException, SQLException {

		Connection connect = null;

		String url = "jdbc:mysql://localhost:3306/cardb";

		String username = "pankaj";
		String password = "pankaj123";

		try {

			Class.forName("com.mysql.jdbc.Driver");

			connect = DriverManager.getConnection(url, username, password);
			// System.out.println("Connection established"+connect);

		} catch (SQLException ex) {
			System.out.println("in exec");
			System.out.println(ex.getMessage());
		}

		List<Car> cars = new ArrayList<Car>();
		PreparedStatement pstmt = connect
				.prepareStatement("select car_id, cname, color, speed, Manufactured_Country from Car");
		ResultSet rs = pstmt.executeQuery();

		while (rs.next()) {

			Car car = new Car();
			car.setCid(rs.getInt("car_id"));
			car.setCname(rs.getString("cname"));
			car.setColor(rs.getString("color"));
			car.setSpeed(rs.getInt("speed"));
			car.setMfdctry(rs.getString("Manufactured_Country"));

			cars.add(car);

		}

		// close resources
		rs.close();
		pstmt.close();
		connect.close();

		return cars;

	}

}
Now create JSF view page car.xhtml as shown below.


<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:h="http://java.sun.com/jsf/html">
<h:head>
	<title>JSF JDBC Integration</title>

</h:head>
<h:body>
	<h2>Car Details</h2>
	<h:dataTable value="#{carBean.cars}" var="car" border="2">
		<h:column>
			<f:facet name="header">Car ID</f:facet>
         #{car.cid}
      </h:column>
		<h:column>
			<f:facet name="header">Car Name</f:facet>
         #{car.cname}
      </h:column>
		<h:column>
			<f:facet name="header">Car Color</f:facet>
           #{car.color}
       </h:column>
		<h:column>
			<f:facet name="header">Car Speed</f:facet>
           #{car.speed}
       </h:column>
		<h:column>
			<f:facet name="header">Manufactured Country</f:facet>
           #{car.mfdctry}
       </h:column>
	</h:dataTable>
</h:body>
</html>
Note that we need to include MySQL Connector jar in our project for getting MySQL database connection. So add below dependency in pom.xml file. Add mysql connector jar version according to your MySQL installation.


<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.34</version>
</dependency>
Now run the application and you will see below output response page.

JSF Database MySQL JDBC Example

Finally below image shows the project structure in Eclipse.

JSF Database example, JSF MySQL, JSF JDBC

You can download the project from below link and play around with it to learn more.

Download JSF JDBC Integration Project
