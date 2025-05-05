<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Student Portal</title>
<style>
  :root {
    --primary-color: #3f51b5;
    --secondary-color: #f50057;
    --background-color: #f0f2f5;
    --text-color: #202124;
    --font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }
  * {
    box-sizing: border-box;
  }
  body {
    margin: 0; padding: 20px;
    font-family: var(--font-family);
    background-color: var(--background-color);
    color: var(--text-color);
    max-width: 350px;
    min-height: 600px;
    margin-left: auto;
    margin-right: auto;
  }
  h1 {
    text-align: center;
    color: var(--primary-color);
    margin-bottom: 20px;
  }
  form {
    background: white;
    padding: 15px 20px;
    border-radius: 10px;
    box-shadow: 0 2px 8px rgba(63,81,181,0.2);
    margin-bottom: 25px;
  }
  label {
    display: block;
    margin-bottom: 6px;
    font-weight: 600;
  }
  input[type="text"],
  input[type="email"],
  select {
    width: 100%;
    padding: 8px 10px;
    margin-bottom: 15px;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-size: 1rem;
  }
  button {
    background-color: var(--primary-color);
    color: white;
    border: none;
    padding: 10px 15px;
    font-weight: 700;
    font-size: 1rem;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    width: 100%;
  }
  button:hover {
    background-color: #32408f;
  }
  #student-list {
    background: white;
    border-radius: 10px;
    box-shadow: 0 2px 8px rgba(63,81,181,0.1);
    padding: 15px 20px;
  }
  .student-item {
    border-bottom: 1px solid #ddd;
    padding: 10px 0;
  }
  .student-item:last-child {
    border-bottom: none;
  }
  .student-name {
    font-weight: 700;
    font-size: 1.1rem;
    color: var(--primary-color);
  }
  .student-info {
    font-size: 0.9rem;
    margin-top: 2px;
  }
  .no-students {
    text-align: center;
    color: #888;
    padding: 15px 0;
  }
  @media (max-width: 400px) {
    body {
      max-width: 100vw;
      padding: 10px;
      min-height: 100vh;
    }
  }
</style>
</head>
<body>
  <h1>Student Portal</h1>
  <form id="studentForm" aria-label="Student Registration Form">
    <label for="name">Full Name</label>
    <input type="text" id="name" placeholder="Enter full name" required maxlength="100" />

    <label for="email">Email</label>
    <input type="email" id="email" placeholder="Enter email address" required maxlength="100" />

    <label for="enrollment">Enrollment Number</label>
    <input type="text" id="enrollment" placeholder="Enter enrollment number" required maxlength="20" />

    <label for="course">Course</label>
    <select id="course" required>
      <option value="" disabled selected>Select course</option>
      <option value="Computer Science">Computer Science</option>
      <option value="Mathematics">Mathematics</option>
      <option value="Physics">Physics</option>
      <option value="Chemistry">Chemistry</option>
      <option value="Biology">Biology</option>
      <option value="Economics">Economics</option>
    </select>

    <button type="submit">Register Student</button>
  </form>

  <div id="student-list" aria-live="polite" aria-relevant="additions removals">
    <p class="no-students">No students registered yet.</p>
  </div>

<script>
  (() => {
    const form = document.getElementById('studentForm');
    const nameInput = document.getElementById('name');
    const emailInput = document.getElementById('email');
    const enrollmentInput = document.getElementById('enrollment');
    const courseSelect = document.getElementById('course');
    const studentList = document.getElementById('student-list');

    let students = [];

    function saveStudents() {
      localStorage.setItem('students', JSON.stringify(students));
    }

    function loadStudents() {
      const stored = localStorage.getItem('students');
      if (stored) {
        students = JSON.parse(stored);
      }
    }

    function renderStudents() {
      if (students.length === 0) {
        studentList.innerHTML = '<p class="no-students">No students registered yet.</p>';
        return;
      }
      let html = '';
      students.forEach(student => {
        html += `
          <div class="student-item" tabindex="0" aria-label="Student: ${student.name}, Email: ${student.email}, Enrollment number: ${student.enrollment}, Course: ${student.course}">
            <div class="student-name">${student.name}</div>
            <div class="student-info">Email: ${student.email}</div>
            <div class="student-info">Enrollment: ${student.enrollment}</div>
            <div class="student-info">Course: ${student.course}</div>
          </div>
        `;
      });
      studentList.innerHTML = html;
    }

    function validateEmail(email) {
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return re.test(email.toLowerCase());
    }

    form.addEventListener('submit', (e) => {
      e.preventDefault();
      const name = nameInput.value.trim();
      const email = emailInput.value.trim();
      const enrollment = enrollmentInput.value.trim();
      const course = courseSelect.value;

      if (name === '' || email === '' || enrollment === '' || course === '') {
        alert('Please fill out all fields.');
        return;
      }
      if (!validateEmail(email)) {
        alert('Please enter a valid email address.');
        return;
      }

      // Check for duplicate enrollment number
      const exists = students.some(s => s.enrollment.toLowerCase() === enrollment.toLowerCase());
      if (exists) {
        alert('Enrollment number already registered.');
        return;
      }

      students.push({ name, email, enrollment, course });
      saveStudents();
      renderStudents();
      form.reset();
      courseSelect.selectedIndex = 0;
      nameInput.focus();
    });

    function init() {
      loadStudents();
      renderStudents();
    }

    init();
  })();
</script>
</body>
</html>

