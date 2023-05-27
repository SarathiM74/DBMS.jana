# DBMS.jana
1a

CREATE DATABASE student_database;

USE student_database;

CREATE TABLE student (

  student_id INT NOT NULL AUTO_INCREMENT,

  student_name VARCHAR(255) NOT NULL,

  student_age INT NOT NULL,

  student_gender CHAR(1) NOT NULL,

  PRIMARY KEY (student_id)

);

ALTER TABLE student ADD CONSTRAINT student_age_check CHECK (student_age BETWEEN 18 AND 65);

INSERT INTO student (student_name, student_age, student_gender) VALUES

('John Doe', 21, 'M'),

('Jane Doe', 22, 'F'),

('Peter Smith', 23, 'M'),

('Mary Jones', 24, 'F');

UPDATE student SET student_name = 'John Smith' WHERE student_id = 1;

DELETE FROM student WHERE student_id = 3;

1b

CREATE TABLE Students (

  student_id INT PRIMARY KEY,

  name VARCHAR(100),

  marks INT

);

INSERT INTO Students (student_id, name, marks)

VALUES (1, 'John Doe', 40),

       (2, 'Jane Smith', 25),

       (3, 'Robert Johnson', 50),

       (4, 'Emily Davis', 35),

       (5, 'Michael Wilson', 60);

       

 DELIMITER //

CREATE PROCEDURE ApplyModerationStrategy()

BEGIN

  DECLARE done INT DEFAULT FALSE;

  DECLARE student_id INT;

  DECLARE student_marks INT;

  -- Declare cursor to fetch students with more than 30 marks

  DECLARE cur CURSOR FOR

    SELECT student_id, marks

    FROM Students

    WHERE marks > 30;

  -- Declare handlers for exceptions

  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

  -- Open the cursor

  OPEN cur;

  -- Loop through the cursor and apply moderation strategy

  moderation_loop: LOOP

    -- Fetch the next student record

    FETCH cur INTO student_id, student_marks;

    -- Exit the loop if no more records found

    IF done THEN

      LEAVE moderation_loop;

    END IF;

    -- Apply moderation strategy here

    -- Example: Increase marks by 10%

    UPDATE Students

    SET marks = student_marks * 1.1

    WHERE student_id = student_id;

  END LOOP;

  -- Close the cursor

  CLOSE cur;

END //

DELIMITER ;

CALL ApplyModerationStrategy();

SELECT * FROM Students;

2a

CREATE TABLE bus (

  bus_id INT NOT NULL AUTO_INCREMENT,

  bus_name VARCHAR(255) NOT NULL,

  bus_type VARCHAR(255) NOT NULL,

  capacity INT NOT NULL,

  PRIMARY KEY (bus_id)

);

CREATE TABLE route (

  route_id INT NOT NULL AUTO_INCREMENT,

  source VARCHAR(255) NOT NULL,

  destination VARCHAR(255) NOT NULL,

  distance INT NOT NULL,

  PRIMARY KEY (route_id)

);

CREATE TABLE reservation (

  reservation_id INT NOT NULL AUTO_INCREMENT,

  bus_id INT NOT NULL,

  route_id INT NOT NULL,

  passenger_name VARCHAR(255) NOT NULL,

  passenger_contact_number VARCHAR(255) NOT NULL,

  passenger_email VARCHAR(255) NOT NULL,

  reservation_date DATE NOT NULL,

  reservation_status VARCHAR(255) NOT NULL,

  PRIMARY KEY (reservation_id),

  FOREIGN KEY (bus_id) REFERENCES bus (bus_id),

  FOREIGN KEY (route_id) REFERENCES route (route_id)

);

2b

CREATE TABLE Customers (

  customer_id INT PRIMARY KEY,

  name VARCHAR(100),

  email VARCHAR(100)

);

CREATE TABLE Reservations (

  reservation_id INT PRIMARY KEY,

  customer_id INT,

  bus_id INT,

  reservation_date DATE,

  FOREIGN KEY (customer_id) REFERENCES Customers (customer_id)

);

CREATE TABLE Buses (

  bus_id INT PRIMARY KEY,

  bus_name VARCHAR(100),

  capacity INT

);

DELIMITER //

CREATE TRIGGER ReservationCancellationTrigger

AFTER DELETE ON Reservations

FOR EACH ROW

BEGIN

  -- Update the bus capacity when a reservation is canceled

  UPDATE Buses

  SET capacity = capacity + 1

  WHERE bus_id = OLD.bus_id;

END //

DELIMITER ;

DELIMITER //

CREATE TRIGGER ReservationTrigger

AFTER INSERT ON Reservations

FOR EACH ROW

BEGIN

  -- Update the bus capacity when a new reservation is made

  UPDATE Buses

  SET capacity = capacity - 1

  WHERE bus_id = NEW.bus_id;

END //

DELIMITER ;3a

SELECT *

FROM books

WHERE title LIKE '%Machine Learning%';

SELECT *

FROM books

WHERE author = 'Andrew Ng'

AND title LIKE '%Machine Learning%';

3b

create or replace procedure update_books_details

(

    book_id number,

    book_title varchar2(100),

    book_author varchar2(100),

    book_isbn varchar2(13),

    book_category varchar2(100),

    book_quantity number

)

as

begin

    -- Update the book details in the books table.

    update books

    set book_title = :book_title,

        book_author = :book_author,

        book_isbn = :book_isbn,

        book_category = :book_category,

        book_quantity = :book_quantity

    where book_id = :book_id;

    -- Notify the librarian that new books have been purchased.

    dbms_output.put_line('New books have been purchased.');

end;

/
4a

-- Create the Product table

CREATE TABLE product (

  product_id INT NOT NULL AUTO_INCREMENT,

  product_name VARCHAR(255) NOT NULL,

  product_description VARCHAR(255) NOT NULL,

  product_price DECIMAL(10,2) NOT NULL,

  PRIMARY KEY (product_id)

);

-- Create the Sales table

CREATE TABLE sales (

  sales_id INT NOT NULL AUTO_INCREMENT,

  product_id INT NOT NULL,

  sales_date DATE NOT NULL,

  quantity INT NOT NULL,

  total_price DECIMAL(10,2) NOT NULL,

  PRIMARY KEY (sales_id),

  FOREIGN KEY (product_id) REFERENCES product (product_id)

);

-- Create the Purchase table

CREATE TABLE purchase (

  purchase_id INT NOT NULL AUTO_INCREMENT,

  product_id INT NOT NULL,

  purchase_date DATE NOT NULL,

  quantity INT NOT NULL,

  total_price DECIMAL(10,2) NOT NULL,

  PRIMARY KEY (purchase_id),

  FOREIGN KEY (product_id) REFERENCES product (product_id)

);

-- Insert some data into the Product table

INSERT INTO product (product_name, product_description, product_price)

VALUES

('T-shirt', 'A blue t-shirt with a white logo', 10.00),

('Jeans', 'A pair of blue jeans', 20.00),

('Shoes', 'A pair of black shoes', 30.00);

-- Insert some data into the Sales table

INSERT INTO sales (product_id, sales_date, quantity, total_price)

VALUES

(1, '2023-05-26', 1, 10.00),

(2, '2023-05-27', 2, 40.00),

(3, '2023-05-28', 3, 90.00);

-- Insert some data into the Purchase table

INSERT INTO purchase (product_id, purchase_date, quantity, total_price)

VALUES

(1, '2023-05-29', 1, 10.00),

(2, '2023-05-30', 2, 40.00),

(3, '2023-06-01', 3, 90.00);

4b

DELIMITER //

CREATE PROCEDURE AddRecords()

BEGIN

  DECLARE i INT DEFAULT 1;

  -- Add 20 records to the Product table

  WHILE i <= 20 DO

    INSERT INTO product (product_name, product_description, product_price)

    VALUES

    ('Product ' || i, 'Description ' || i, i * 10.00);

    SET i = i + 1;

  END WHILE;

  SET i = 1;

  -- Add 20 records to the Sales table

  WHILE i <= 20 DO

    INSERT INTO sales (product_id, sales_date, quantity, total_price)

    VALUES

    (i, '2023-06-' || i, i, i * 10.00);

    SET i = i + 1;

  END WHILE;

  SET i = 1;

  -- Add 20 records to the Purchase table

  WHILE i <= 20 DO

    INSERT INTO purchase (product_id, purchase_date, quantity, total_price)

    VALUES

    (i, '2023-06-' || i, i, i * 10.00);

    SET i = i + 1;

  END WHILE;

END //

DELIMITER ;

CALL AddRecords();
5a

-- Create the employee_personal table

CREATE TABLE employee_personal (

  employee_id INT PRIMARY KEY,

  first_name VARCHAR(50) NOT NULL,

  last_name VARCHAR(50) NOT NULL,

  birth_date DATE NOT NULL,

  gender VARCHAR(10) NOT NULL,

  address VARCHAR(100) NOT NULL,

  phone_number VARCHAR(20) NOT NULL

);

-- Create the Salary table

CREATE TABLE Salary (

  salary_id INT PRIMARY KEY,

  employee_id INT NOT NULL,

  amount DECIMAL(10,2) NOT NULL,

  date_received DATE NOT NULL,

  FOREIGN KEY (employee_id) REFERENCES employee_personal (employee_id)

);

-- Create the Department table

CREATE TABLE Department (

  department_id INT PRIMARY KEY,

  department_name VARCHAR(50) NOT NULL

);

-- Minimum salary

SELECT MIN(amount) AS min_salary FROM Salary;

-- Maximum salary

SELECT MAX(amount) AS max_salary FROM Salary;

-- Total salary

SELECT SUM(amount) AS total_salary FROM Salary;

-- Average salary

SELECT AVG(amount) AS avg_salary FROM Salary;

5b

DELIMITER //

CREATE FUNCTION UpdateSalary(employeeId INT, incentives DECIMAL(10,2)) RETURNS DECIMAL(10,2)

BEGIN

  DECLARE currentSalary DECIMAL(10,2);

  -- Get the current salary of the employee

  SELECT amount INTO currentSalary FROM Salary WHERE employee_id = employeeId;

  -- Calculate the updated salary

  SET currentSalary = currentSalary + incentives;

  -- Update the salary in the Salary table

  UPDATE Salary SET amount = currentSalary WHERE employee_id = employeeId;

  -- Return the updated salary

  RETURN currentSalary;

END //

DELIMITER ;

UPDATE Salary SET amount = UpdateSalary(employee_id, incentives) WHERE employee_id = <employee_id>;
6a

-- Create the Users table

CREATE TABLE Users (

  user_id INT AUTO_INCREMENT PRIMARY KEY,

  username VARCHAR(50) NOT NULL,

  email VARCHAR(100) NOT NULL,

  password VARCHAR(100) NOT NULL

);

-- Create the Products table

CREATE TABLE Products (

  product_id INT AUTO_INCREMENT PRIMARY KEY,

  product_name VARCHAR(100) NOT NULL,

  price DECIMAL(10, 2) NOT NULL,

  description TEXT

);

-- Create the Orders table

CREATE TABLE Orders (

  order_id INT AUTO_INCREMENT PRIMARY KEY,

  user_id INT NOT NULL,

  product_id INT NOT NULL,

  quantity INT NOT NULL,

  order_date DATE NOT NULL,

  FOREIGN KEY (user_id) REFERENCES Users (user_id),

  FOREIGN KEY (product_id) REFERENCES Products (product_id)

);

6b

-- Insert sample users

INSERT INTO Users (username, email, password)

VALUES

('john', 'john@example.com', 'password123'),

('emma', 'emma@example.com', 'pass456'),

('michael', 'michael@example.com', 'secure789');

-- Insert sample products

INSERT INTO Products (product_name, price, description)

VALUES

('T-shirt', 19.99, 'A comfortable cotton t-shirt'),

('Jeans', 39.99, 'Stylish denim jeans'),

('Shoes', 59.99, 'Durable sports shoes');

-- Insert sample orders

INSERT INTO Orders (user_id, product_id, quantity, order_date)

VALUES

(1, 1, 2, '2023-05-01'),

(2, 3, 1, '2023-05-02'),

(3, 2, 3, '2023-05-03');

SELECT *

FROM Products

WHERE product_id IN (

  SELECT product_id

  FROM Orders

  WHERE user_id = 1

);

-- Procedure to display available items before purchase

DELIMITER //

CREATE PROCEDURE display_available_items_before_purchase()

BEGIN

    SELECT *

    FROM Products WHERE product_name=T-shirt;

END //

DELIMITER ;

-- Procedure to display available items after purchase

DELIMITER //

CREATE PROCEDURE display_available_items_after_purchase()

BEGIN

    SELECT *

    FROM Products WHERE product_name=jeans,shoes;

END //

DELIMITER ;

CALL display_available_items_before_purchase();
7a

CREATE DATABASE wedding_hall_reservation;

USE wedding_hall_reservation;

CREATE TABLE halls (

  hall_id INT NOT NULL AUTO_INCREMENT,

  hall_name VARCHAR(255) NOT NULL,

  capacity INT NOT NULL,

  price INT NOT NULL,

  PRIMARY KEY (hall_id)

);

CREATE TABLE bookings (

  booking_id INT NOT NULL AUTO_INCREMENT,

  hall_id INT NOT NULL,

  customer_name VARCHAR(255) NOT NULL,

  booking_date DATE NOT NULL,

  booking_time TIME NOT NULL,

  PRIMARY KEY (booking_id),

  FOREIGN KEY (hall_id) REFERENCES halls (hall_id)

);

INSERT INTO halls (hall_name, capacity, price) VALUES

('Grand Hall', 500, 1000),

('Ballroom', 250, 500),

('Garden Room', 100, 250);

INSERT INTO bookings (hall_id, customer_name, booking_date, booking_time) VALUES

(1, 'John Smith', '2023-06-01', '12:00'),

(2, 'Jane Doe', '2023-06-02', '18:00'),

(3, 'Peter Jones', '2023-06-03', '20:00');

SELECT halls.hall_name, halls.capacity, halls.price, bookings.booking_date, bookings.booking_time

FROM halls

INNER JOIN bookings

ON halls.hall_id = bookings.hall_id;

7b

DELIMITER $$

CREATE PROCEDURE avail_reduction (hall_id INT, customer_name VARCHAR(255))

BEGIN

  DECLARE price INT;

  SELECT price INTO price

  FROM halls

  WHERE hall_id = hall_id;

  IF customer_name IN ('John Smith', 'Jane Doe', 'Peter Jones') THEN

    UPDATE bookings

    SET price = price * 0.9

    WHERE hall_id = hall_id

    AND customer_name = customer_name;

  END IF;

END $$

DELIMITER ;
CALL display_available_items_after_purchase();
8a

-- Create the database

CREATE DATABASE car_manufacturing_company;

-- Use the database

USE car_manufacturing_company;

-- Create the tables

CREATE TABLE cars (

  id INT NOT NULL AUTO_INCREMENT,

  model VARCHAR(255) NOT NULL,

  price INT NOT NULL,

  PRIMARY KEY (id)

);

-- Insert some data into the tables

INSERT INTO cars (model, price) VALUES

('Honda Civic', 25000),

('Toyota Corolla', 20000),

('Nissan Altima', 22000);

-- Use the having clause to display the car models based on price

SELECT model, price

FROM cars

GROUP BY model

HAVING price > 20000;

8b

DELIMITER //

CREATE PROCEDURE insert_car (

    nmodel VARCHAR(255),

    nprice INT

)

AS

BEGIN

    INSERT INTO cars (model, price) VALUES (nmodel, nprice);

END;

CREATE PROCEDURE update_car (

    nid INT,

    nmodel VARCHAR(255),

    nprice INT

)

AS

BEGIN

    UPDATE cars

    SET model = nmodel,

        price = nprice

    WHERE id = nid;

END //

DELIMITER ;
9a

-- Create the database

CREATE DATABASE school;

-- Use the database

USE school;

-- Create the tables

CREATE TABLE students (

  id INT NOT NULL AUTO_INCREMENT,

  name VARCHAR(255) NOT NULL,

  age INT NOT NULL,

  PRIMARY KEY (id)

);

CREATE TABLE courses (

  id INT NOT NULL AUTO_INCREMENT,

  name VARCHAR(255) NOT NULL,

  description VARCHAR(255) NOT NULL,

  PRIMARY KEY (id)

);

-- Insert some data into the tables

INSERT INTO students (name, age) VALUES

('John Doe', 20),

('Jane Doe', 18),

('Peter Smith', 19);

INSERT INTO courses (name, description) VALUES

('Math', 'A course on mathematics'),

('English', 'A course on English'),

('Science', 'A course on science');

-- Use various join operations to display the records

-- Inner join

SELECT s.name, c.name

FROM students s

INNER JOIN courses c

ON s.id = c.id;

-- Left join

SELECT s.name, c.name

FROM students s

LEFT JOIN courses c

ON s.id = c.id;

-- Right join

SELECT s.name, c.name

FROM students s

RIGHT JOIN courses c

ON s.id = c.id;

-- Full join

SELECT s.name, c.name

FROM students s

FULL JOIN courses c

ON s.id = c.id;

9b

DELIMITER //CREATE PROCEDURE getDayName(day INT)

BEGIN

  DECLARE dayName VARCHAR(255);

  SET dayName = (CASE day

    WHEN 1 THEN 'Sunday'

    WHEN 2 THEN 'Monday'

    WHEN 3 THEN 'Tuesday'

    WHEN 4 THEN 'Wednesday'

    WHEN 5 THEN 'Thursday'

    WHEN 6 THEN 'Friday'

    WHEN 7 THEN 'Saturday'

    END);

  SELECT dayName;

END //

DELIMITER  ;
