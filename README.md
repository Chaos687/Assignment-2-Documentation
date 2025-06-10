
# Newman Junior Soccer Association Website Documentation

**Live Site**: [https://newmanjuniorsoccerassociation.click](https://newmanjuniorsoccerassociation.click)

This guide documents the full setup of the NJSA website hosted on an AWS EC2 instance.

---

## Table of Contents

1. EC2 Apache Web Server Setup
2. EC2 SSH Access
3. Installed Apache Web Server 
4. Set Document Root & Enable SSL
5. Generate & Install SSL Certificate
6. Configure Apache for SSL
7. Test Apache Configuration & Restart
8. Editing the 3 Web Pages - Home, About, Contact

---

## 1. EC2 Apache Web Server Setup

**Launching an Amazon EC2 instance:**

- **AMI**: Ubuntu 20.04 LTS (Free Tier)
- **Instance Type**: t2.micro (Free Tier Eligible)
- **Storage**: Default (8 GB)
- **Security Group**: Name it `ssh-and-web`
- **Add rule**: HTTP

**Create a key pair:**

My SSH key `AWSwebserver-key.pem` is located on my desktop.

---

## 2. EC2 SSH Access

```bash
cd Desktop
ls -la AWSwebserver-key.pem
chmod 400 AWSwebserver-key.pem
```

Connect to the EC2 instance via SSH:

```bash
ssh -i "AWSwebserver-key.pem" ubuntu@ec2-16-176-9-20.ap-southeast-2.compute.amazonaws.com
```

---

## 3. Installed Apache Web Server

```bash
sudo apt update
sudo apt install apache2
```

---

## 4. Set the Correct Document Root & Enable SSL

```bash
sudo nano /etc/apache2/sites-enabled/default-ssl.conf
```

Change DocumentRoot to:

```apache
DocumentRoot /var/www/html/
```

Enable and reload apache:

```bash
sudo systemctl reload apache2
sudo a2enmod ssl
sudo systemctl restart apache2
```

---

## 5. Generate & Install SSL Certificate

- Files received from SSL provider:
    - `/etc/ssl/certificate.crt`
    - `/etc/ssl/private.key`
    - `/etc/ssl/ca_bundle.crt`

Give correct permissions:

```bash
sudo chmod 600 /etc/ssl/private.key
sudo chown root:root /etc/ssl/*
```

---

## 6. Configure Apache

Edit your SSL-enabled virtual host (usually in `default-ssl.conf`):

```apache
<VirtualHost *:443>
    DocumentRoot /var/www/html/
    ServerName www.newmanjuniorsoccerassociation.click
    ServerAlias newmanjuniorsoccerassociation.click

    SSLEngine on
    SSLCertificateFile /etc/ssl/certificate.crt
    SSLCertificateKeyFile /etc/ssl/private.key
    SSLCertificateChainFile /etc/ssl/ca_bundle.crt
</VirtualHost>
```

---

## 7. Test Apache Configuration & Restart

```bash
sudo apache2ctl configtest
sudo systemctl restart apache2
```

**Domain name server check:**

```bash
whois newmanjuniorsoccerassociation.click | grep "Name Server"
```

Should match AWS Route 53:

```
ns-80.awsdns-10.com  
ns-1925.awsdns-48.co.uk  
ns-1092.awsdns-08.org  
ns-798.awsdns-35.net
```

---

## 8. Editing the 3 Web Pages

---

### HOME PAGE: `index.html`

To edit:

```bash
sudo nano /var/www/html/index.html
```

### ABOUT PAGE: `about.html`

To edit:

```bash
sudo nano /var/www/html/about.html
```

### CONTACT PAGE: `contact.html`

To edit:

```bash
sudo nano /var/www/html/contact.html
```

#### Navigation Bar (with hover effect)

```html
<nav>
  <a href="index.html">Home</a>
  <a href="about.html">About</a>
  <a href="contact.html">Contact</a>
</nav>
```

CSS for hover:

```css
nav a:hover {
  background-color: #ddd;
  color: black;
}
```

#### Header with logo (used on all pages)

```html
<header>
  <img src="https://github.com/Chaos687/njsa-assets/blob/main/278396679_5405959209436215_9109305474040067837_n.jpg?raw=true" alt="NJSA Logo" class="logo">
  <h1>Newman Junior Soccer Association</h1>
</header>
```

#### Home Page Banner (used on all pages)

```html
<section id="home">
  <h2>Welcome to NJSA!</h2>
  <p>Building community through youth soccer in Newman.</p>
  <img src="https://github.com/Chaos687/njsa-assets/blob/main/434082376_908573474401029_8717107320456803714_n.png?raw=true" alt="NJSA Banner" style="width:100%; border-radius: 10px; margin-top: 10px;">
</section>
```

#### Footer with Google Maps and Scroll Detection (used on all pages)

```html
<footer id="pageFooter">
  <div style="flex: 1 1 300px; min-width: 280px; display: flex; flex-direction: column; gap: 6px; text-align: left;">
    <div>
      <strong>Newman Junior Soccer Association</strong><br>
      NJSA
    </div>
    <div>
      Capricorn Oval, Capricorn Sporting Complex,<br>
      Fortescue Avenue<br>
      Newman, Western Australia, 6753<br>
      Australia
    </div>
    <div style="margin-top: 8px;">
      &copy; 2025 Newman Junior Soccer Association
    </div>
  </div>
  <div style="
    flex: 0 1 300px;
    max-width: 300px;
    border-radius: 15px;
    overflow: hidden;
    box-shadow: 0 2px 8px rgba(0,0,0,0.3);">
    <iframe
      src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3236.0039405795337!2d119.73335161526105!3d-23.36169418075262!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x2b93b4e6bb0a0f67%3A0xe2cac18a51c587d8!2sCapricorn%20Oval!5e0!3m2!1sen!2sau!4v1716825301440!5m2!1sen!2sau"
      width="100%"
      height="150"
      style="border:0; display:block;"
      allowfullscreen=""
      loading="lazy"
      referrerpolicy="no-referrer-when-downgrade">
    </iframe>
  </div>
</footer>
<script>
  const footer = document.getElementById('pageFooter');
  function checkScroll() {
    const scrollPosition = window.scrollY + window.innerHeight;
    const nearBottom = document.documentElement.scrollHeight - scrollPosition < 50;
    if (nearBottom) {
      footer.classList.remove('hidden');
    } else {
      footer.classList.add('hidden');
    }
  }
  window.addEventListener('scroll', checkScroll);
  window.addEventListener('resize', checkScroll);
  checkScroll();
</script>
```

---

## Full Home Page: `index.html`
---
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Home - Newman Junior Soccer Association</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; padding: 0; }
    header {
      background-color: #4CAF50;
      color: white;
      padding: 20px;
      display: flex;
      align-items: center;
      text-align: left;
    }
    .logo { height: 60px; width: auto; margin-right: 15px; border-radius: 5px; }
    header h1 { margin: 0; font-size: 24px; }
    nav { background-color: #333; overflow: hidden; }
    nav a { float: left; color: white; text-align: center; padding: 14px 16px; text-decoration: none; }
    nav a:hover { background-color: #ddd; color: black; }
    section { padding: 20px; }
    footer {
      position: fixed;
      bottom: 0;
      left: 0;
      width: 100%;
      background-color: #4CAF50;
      color: white;
      padding: 10px 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
      box-sizing: border-box;
      max-width: 100vw;
      overflow-x: hidden;
      z-index: 1000;
      font-family: Arial, sans-serif;
      font-size: 14px;
      line-height: 1.3;
      transition: transform 0.3s ease;
      transform: translateY(0);
    }
    footer.hidden { transform: translateY(100%); }
    body { padding-bottom: 180px; margin: 0; box-sizing: border-box; }
  </style>
</head>
<body>
  <header>
    <img src="https://github.com/Chaos687/njsa-assets/blob/main/278396679_5405959209436215_9109305474040067837_n.jpg?raw=true" alt="NJSA Logo" class="logo">
    <h1>Newman Junior Soccer Association</h1>
  </header>
  <nav>
    <a href="index.html">Home</a>
    <a href="about.html">About</a>
    <a href="contact.html">Contact</a>
  </nav>
  <section id="home">
    <h2>Welcome to NJSA!</h2>
    <p>Building community through youth soccer in Newman.</p>
    <img src="https://github.com/Chaos687/njsa-assets/blob/main/434082376_908573474401029_8717107320456803714_n.png?raw=true" alt="NJSA Banner" style="width:100%; border-radius: 10px; margin-top: 10px;">
  </section>
  <footer id="pageFooter">
    <div style="flex: 1 1 300px; min-width: 280px; display: flex; flex-direction: column; gap: 6px; text-align: left;">
      <div>
        <strong>Newman Junior Soccer Association</strong><br>
        NJSA
      </div>
      <div>
        Capricorn Oval, Capricorn Sporting Complex,<br>
        Fortescue Avenue<br>
        Newman, Western Australia, 6753<br>
        Australia
      </div>
      <div style="margin-top: 8px;">
        &copy; 2025 Newman Junior Soccer Association
      </div>
    </div>
    <div style="flex: 0 1 300px; max-width: 300px; border-radius: 15px; overflow: hidden; box-shadow: 0 2px 8px rgba(0,0,0,0.3);">
      <iframe
        src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3236.0039405795337!2d119.73335161526105!3d-23.36169418075262!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x2b93b4e6bb0a0f67%3A0xe2cac18a51c587d8!2sCapricorn%20Oval!5e0!3m2!1sen!2sau!4v1716825301440!5m2!1sen!2sau"
        width="100%"
        height="150"
        style="border:0; display:block;"
        allowfullscreen=""
        loading="lazy"
        referrerpolicy="no-referrer-when-downgrade">
      </iframe>
    </div>
  </footer>
  <script>
    const footer = document.getElementById('pageFooter');
    function checkScroll() {
      const scrollPosition = window.scrollY + window.innerHeight;
      const nearBottom = document.documentElement.scrollHeight - scrollPosition < 50;
      if (nearBottom) {
        footer.classList.remove('hidden');
      } else {
        footer.classList.add('hidden');
      }
    }
    window.addEventListener('scroll', checkScroll);
    window.addEventListener('resize', checkScroll);
    checkScroll();
  </script>
</body>
</html>
```

---

### ABOUT PAGE: `about.html`

#### Intergrated Video Explainer covering the layout of the website:
 
  <video width="640" height="360" controls>
  <source src="https://raw.githubusercontent.com/Chaos687/Assignment-2-Documentation/main/Website%20video%20explainer.mp4" type="video/mp4">
  Your browser does not support the video tag.
  </video>
   <video width="640" height="360" controls>
   <source src="https://raw.githubusercontent.com/Chaos687/Assignment-2-Documentation/main/Website%20video%20explainer.mp4" type="video/mp4">
   </video>
---

#### Carousel Section:

```css
.carousel-wrapper {
  overflow: hidden;
  width: 100%;
  height: 220px;
  position: relative;
  box-sizing: border-box;
  margin: 20px 0;
}
.carousel-track {
  display: flex;
  width: fit-content;
  animation: scrollLeft 30s linear infinite;
}
.carousel-track img {
  width: 200px;
  height: 200px;
  object-fit: cover;
  border-radius: 10px;
  margin-right: 10px;
  flex-shrink: 0;
}
@keyframes scrollLeft {
  0% { transform: translateX(0%); }
  100% { transform: translateX(-50%); }
}
```

#### Carousel Section:

```css
.carousel-wrapper {
  overflow: hidden;
  width: 100%;
  height: 220px;
  position: relative;
  box-sizing: border-box;
  margin: 20px 0;
}
.carousel-track {
  display: flex;
  width: fit-content;
  animation: scrollLeft 30s linear infinite;
}
.carousel-track img {
  width: 200px;
  height: 200px;
  object-fit: cover;
  border-radius: 10px;
  margin-right: 10px;
  flex-shrink: 0;
}
@keyframes scrollLeft {
  0% { transform: translateX(0%); }
  100% { transform: translateX(-50%); }
}
```
<section>
  <div class="carousel-wrapper">
    <div class="carousel-track">
      <!-- First Set -->
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/495460118_1214606987131008_5803292666007159757_n.jpg?raw=true" alt="Gallery 1" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/493405774_1214606923797681_7593303036252490454_n.jpg?raw=true" alt="Gallery 2" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7631.jpg?raw=true" alt="Gallery 3" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7632.jpg?raw=true" alt="Gallery 4" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/NJSA.jpg?raw=true" alt="Gallery 5" />
      <!-- Duplicate Set for Seamless Loop -->
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/495460118_1214606987131008_5803292666007159757_n.jpg?raw=true" alt="Gallery 1" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/493405774_1214606923797681_7593303036252490454_n.jpg?raw=true" alt="Gallery 2" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7631.jpg?raw=true" alt="Gallery 3" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7632.jpg?raw=true" alt="Gallery 4" />
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/NJSA.jpg?raw=true" alt="Gallery 5" />
    </div>
  </div>
</section>
```

---

## Full About Page: `about.html`
---
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>About Us - Newman Junior Soccer Association</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; padding: 0; }
    header {
      background-color: #4CAF50;
      color: white;
      padding: 20px;
      display: flex;
      align-items: center;
      text-align: left;
    }
    .logo { height: 60px; width: auto; margin-right: 15px; border-radius: 5px; }
    header h1 { margin: 0; font-size: 24px; }
    nav { background-color: #333; overflow: hidden; }
    nav a { float: left; color: white; text-align: center; padding: 14px 16px; text-decoration: none; }
    nav a:hover { background-color: #ddd; color: black; }
    section { padding: 20px; }
    .carousel-wrapper {
      overflow: hidden;
      width: 100%;
      height: 220px;
      position: relative;
      box-sizing: border-box;
      margin: 20px 0;
    }
    .carousel-track {
      display: flex;
      width: fit-content;
      animation: scrollLeft 30s linear infinite;
    }
    .carousel-track img {
      width: 200px;
      height: 200px;
      object-fit: cover;
      border-radius: 10px;
      margin-right: 10px;
      flex-shrink: 0;
    }
    @keyframes scrollLeft {
      0% { transform: translateX(0%); }
      100% { transform: translateX(-50%); }
    }
    footer {
      position: fixed;
      bottom: 0;
      left: 0;
      width: 100%;
      background-color: #4CAF50;
      color: white;
      padding: 10px 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
      box-sizing: border-box;
      max-width: 100vw;
      overflow-x: hidden;
      z-index: 1000;
      font-family: Arial, sans-serif;
      font-size: 14px;
      line-height: 1.3;
      transition: transform 0.3s ease;
      transform: translateY(0);
    }
    footer.hidden { transform: translateY(100%); }
    body { padding-bottom: 180px; margin: 0; box-sizing: border-box; }
  </style>
</head>
<body>
  <header>
    <img src="https://github.com/Chaos687/njsa-assets/blob/main/278396679_5405959209436215_9109305474040067837_n.jpg?raw=true" alt="NJSA Logo" class="logo">
    <h1>Newman Junior Soccer Association</h1>
  </header>
  <nav>
    <a href="index.html">Home</a>
    <a href="about.html">About</a>
    <a href="contact.html">Contact</a>
  </nav>
  <section>
    <h2>About Us</h2>
    <p>
      Newman Junior Soccer Association (NJSA) is a long-standing junior sporting association located in Newman, a regional area within Western Australia with a residential population of 6,761 (ABS Census Data, 2021).
      Due to the isolated nature of living in remote areas, sporting groups like NJSA are essential to increase liveability,
      create a sense of community, promote healthy living through physical activity and continue to provide development pathways for peak-performing athletes.
    </p>
  </section>
<<<<<<< HEAD
  <video width="640" height="360" controls>
  <source src="https://raw.githubusercontent.com/Chaos687/Assignment-2-Documentation/main/Website%20video%20explainer.mp4" type="video/mp4">
  Your browser does not support the video tag.
  </video>
=======
>>>>>>> 2a90ce714beb409f13e066201da998dea55b768c

    <video width="640" height="360" controls>
    <source src="https://raw.githubusercontent.com/Chaos687/Assignment-2-Documentation/main/Website%20video%20explainer.mp4" type="video/mp4">
    </video>
 
  <section>
    <div class="carousel-wrapper">
      <div class="carousel-track">
        <!-- First Set -->
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/495460118_1214606987131008_5803292666007159757_n.jpg?raw=true" alt="Gallery 1" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/493405774_1214606923797681_7593303036252490454_n.jpg?raw=true" alt="Gallery 2" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7631.jpg?raw=true" alt="Gallery 3" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7632.jpg?raw=true" alt="Gallery 4" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/NJSA.jpg?raw=true" alt="Gallery 5" />
        <!-- Duplicate Set for Seamless Loop -->
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/495460118_1214606987131008_5803292666007159757_n.jpg?raw=true" alt="Gallery 1" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/493405774_1214606923797681_7593303036252490454_n.jpg?raw=true" alt="Gallery 2" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7631.jpg?raw=true" alt="Gallery 3" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/IMG_7632.jpg?raw=true" alt="Gallery 4" />
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/NJSA.jpg?raw=true" alt="Gallery 5" />
      </div>
    </div>
  </section>
  <footer id="pageFooter">
    <div style="flex: 1 1 300px; min-width: 280px; display: flex; flex-direction: column; gap: 6px; text-align: left;">
      <div>
        <strong>Newman Junior Soccer Association</strong><br>
        NJSA
      </div>
      <div>
        Capricorn Oval, Capricorn Sporting Complex,<br>
        Fortescue Avenue<br>
        Newman, Western Australia, 6753<br>
        Australia
      </div>
      <div style="margin-top: 8px;">
        &copy; 2025 Newman Junior Soccer Association
      </div>
    </div>
    <div style="flex: 0 1 300px; max-width: 300px; border-radius: 15px; overflow: hidden; box-shadow: 0 2px 8px rgba(0,0,0,0.3);">
      <iframe
        src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3236.0039405795337!2d119.73335161526105!3d-23.36169418075262!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x2b93b4e6bb0a0f67%3A0xe2cac18a51c587d8!2sCapricorn%20Oval!5e0!3m2!1sen!2sau!4v1716825301440!5m2!1sen!2sau"
        width="100%"
        height="150"
        style="border:0; display:block;"
        allowfullscreen=""
        loading="lazy"
        referrerpolicy="no-referrer-when-downgrade">
      </iframe>
    </div>
  </footer>
  <script>
    const footer = document.getElementById('pageFooter');
    function checkScroll() {
      const scrollPosition = window.scrollY + window.innerHeight;
      const nearBottom = document.documentElement.scrollHeight - scrollPosition < 50;
      if (nearBottom) {
        footer.classList.remove('hidden');
      } else {
        footer.classList.add('hidden');
      }
    }
    window.addEventListener('scroll', checkScroll);
    window.addEventListener('resize', checkScroll);
    checkScroll();
  </script>
</body>
</html>
```

## CONTACT PAGE: `contact.html`

---

### Contact Form and Contact Info Section

**This code below generates the “Contact Us” form (does not actually work when used, but can enter text into text boxes) and the contact info section containing multiple fake emails and names of fictional individuals working for NJSA:**

#### CSS for Contact Form and Contact Info

```css
.form-section, .contact-section {
  width: 45%; 
  min-width: 300px; 
}

h2 {
  margin-bottom: 20px; 
}

input[type="text"],
input[type="email"],
textarea {
  width: 100%; 
  padding: 10px;
  margin-bottom: 15px; 
  border: 1px solid #ccc;
  border-radius: 4px; 
  font-size: 14px; 
}

textarea {
  height: 100px; 
}

button {
  padding: 10px 20px; 
  background-color: #5ca145; 
  color: white; 
  border: none; 
  border-radius: 4px; 
  cursor: pointer; 
}

.contact-info h3 {
  margin-bottom: 10px; 
}

.contact-row {
  display: flex;
  justify-content: space-between; 
  border-bottom: 1px solid #ccc; 
  padding: 6px 0; 
  font-size: 14px; 
  flex-wrap: wrap; 
}

.contact-info a {
  color: green;
  text-decoration: none;
}

.contact-info a:hover {
  text-decoration: underline;
}

.logos {
  display: flex; 
  justify-content: center; 
  gap: 40px; 
  margin-top: 40px; 
}

.logos img {
  height: 40px; 
}
```

#### Contact Section HTML

```html
<section>
  <h2>Contact Us</h2> <!-- Section title -->

  <div class="container">
    <!-- Form on the left -->
    <div class="form-section">
      <form>
        <input type="text" placeholder="Name *" required> <!-- Name field -->
        <input type="text" placeholder="Phone *" required> <!-- Phone field -->
        <input type="email" placeholder="Email *" required> <!-- Email field -->
        <textarea placeholder="Message *" required></textarea> <!-- Message box -->
        <button type="submit">Send</button> <!-- Submit button -->
      </form>
    </div>
    <!-- Contact details on the right -->
    <div class="contact-section">
      <h3>Newman Junior Soccer Association</h3>
      <p>
        Kangaroo Oval, Capricorn Sporting Complex, Fortescue Avenue<br>
        Newman, Western Australia, 6753<br>
        Australia
      </p>
      <!-- Each contact row shows Name, Role, and Email -->
      <div class="contact-info">
        <h3>Contacts</h3>
        <div class="contact-row"><div>Tahnee Bonney</div><div>President</div><div><a href="mailto:president.njsa@hotmail.com">president.njsa@gmail.com</a></div></div>
        <div class="contact-row"><div>Soul Jones</div><div>Vice President</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
        <div class="contact-row"><div>Rosarita Taranto</div><div>Secretary</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
        <div class="contact-row"><div>Grim Reutens</div><div>Treasurer</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
        <div class="contact-row"><div>Yase Dandy</div><div>Registrar</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
      </div>
      <h3 style="margin-top: 20px;">Correspondence</h3>
      <p>PO Box 109 Newman WA 6753</p>
    </div>
  </div>
</section>
```

---

### *Sponsor logos section (not visible on the site, can be ignored):*

```html
<div class="logos">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/58/Dot_painting_example.svg/1200px-Dot_painting_example.svg.png" alt="KingKira Logo">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Breight.png/600px-Breight.png" alt="Breight Logo">
</div>
```

---

## Full Contact Page: `contact.html`
---
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Contact Us - Newman Junior Soccer Association</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    header {
      background-color: #4CAF50;
      color: white;
      padding: 20px;
      display: flex;
      align-items: center;
      text-align: left;
    }

    .logo {
      height: 60px;
      width: auto;
      margin-right: 15px;
      border-radius: 5px;
    }

    header h1 {
      margin: 0;
      font-size: 24px;
    }

    nav {
      background-color: #333;
      overflow: hidden;
    }

    nav a {
      float: left;
      color: white;
      text-align: center;
      padding: 14px 16px;
      text-decoration: none;
    }

    nav a:hover {
      background-color: #ddd;
      color: black;
    }

    section {
      padding: 40px 20px 200px;
    }

    .container {
      display: flex;
      justify-content: space-between;
      flex-wrap: wrap;
    }

    .form-section, .contact-section {
      width: 45%;
      min-width: 300px;
    }

    h2 {
      margin-bottom: 20px;
    }

    input[type="text"],
    input[type="email"],
    textarea {
      width: 100%;
      padding: 10px;
      margin-bottom: 15px;
      border: 1px solid #ccc;
      border-radius: 4px;
      font-size: 14px;
    }

    textarea {
      height: 100px;
    }

    button {
      padding: 10px 20px;
      background-color: #5ca145;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }

    .contact-info h3 {
      margin-bottom: 10px;
    }

    .contact-row {
      display: flex;
      justify-content: space-between;
      border-bottom: 1px solid #ccc;
      padding: 6px 0;
      font-size: 14px;
      flex-wrap: wrap;
    }
    .contact-info a {
      color: green;
      text-decoration: none;
    }
    .contact-info a:hover {
      text-decoration: underline;
    }

    .logos {
      display: flex;
      justify-content: center;
      gap: 40px;
      margin-top: 40px;
    }

    .logos img {
      height: 40px;
    }

    footer {
      position: fixed;
      bottom: 0;
      left: 0;
      width: 100%;
      background-color: #4CAF50;
      color: white;
      padding: 10px 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
      box-sizing: border-box;
      max-width: 100vw;
      overflow-x: hidden;
      z-index: 1000;
      font-family: Arial, sans-serif;
      font-size: 14px;
      line-height: 1.3;
      transition: transform 0.3s ease;
      transform: translateY(0);
    }

    footer.hidden {
      transform: translateY(100%);
    }
  </style>
</head>
<body>

<header>
  <img class="logo" src="https://github.com/Chaos687/njsa-assets/blob/main/278396679_5405959209436215_9109305474040067837_n.jpg?raw=true" alt="NJSA Logo">
  <h1>Newman Junior Soccer Association</h1>
</header>

<nav>
  <a href="index.html">Home</a>
  <a href="about.html">About</a>
  <a href="contact.html">Contact</a>
</nav>
<section>
  <h2>Contact Us</h2>
  <div class="container">
    <div class="form-section">
      <form>
        <input type="text" placeholder="Name *" required>
        <input type="text" placeholder="Phone *" required>
        <input type="email" placeholder="Email *" required>
        <textarea placeholder="Message *" required></textarea>
        <button type="submit">Send</button>
      </form>
    </div>

    <div class="contact-section">
      <h3>Newman Junior Soccer Association</h3>
      <p>Kangaroo Oval, Capricorn Sporting Complex, Fortescue Avenue<br>
        Newman, Western Australia, 6753<br>Australia</p>

      <div class="contact-info">
        <h3>Contacts</h3>
        <div class="contact-row"><div>Tahnee Bonney</div>      <div>President</div><div><a href="mailto:president.njsa@hotmail.com">president.njsa@gmail.com</a></div></div>
        <div class="contact-row"><div>Soul Jones</div>         <div>Vice President</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
        <div class="contact-row"><div>Rosarita Taranto</div>   <div>Secretary</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
        <div class="contact-row"><div>Grim Reutens</div>       <div>Treasurer</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
        <div class="contact-row"><div>Yase Dandy</div>         <div>Registrar</div><div><a href="mailto:newmanjuniorsoccer@hotmail.com">newmanjuniorsoccer@gmail.com</a></div></div>
      </div>

      <h3 style="margin-top: 20px;">Correspondence</h3>
      <p>PO Box 109 Newman WA 6753</p>
    </div>
  </div>
</section>

<div class="logos">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/58/Dot_painting_example.svg/1200px-Dot_painting_example.svg.png" alt="KingKira Logo">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Breight.png/600px-Breight.png" alt="Breight Logo">
</div>

<footer id="pageFooter">
  <div style="flex: 1 1 300px; min-width: 280px; display: flex; flex-direction: column; gap: 6px; text-align: left;">
    <div>
      <strong>Newman Junior Soccer Association</strong><br>
      NJSA
    </div>
    <div>
      Capricorn Oval, Capricorn Sporting Complex,<br>
      Fortescue Avenue<br>
      Newman, Western Australia, 6753<br>
      Australia
    </div>
    <div style="margin-top: 8px;">
      &copy; 2025 Newman Junior Soccer Association
    </div>
  </div>
  <div style="flex: 0 1 300px; max-width: 300px; border-radius: 15px; overflow: hidden; box-shadow: 0 2px 8px rgba(0,0,0,0.3);">
    <iframe
      src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3236.0039405795337!2d119.73335161526105!3d-23.36169418075224!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x2bfec4bd3eb8ad8b%3A0x908d28cc6c02d96b!2sCapricorn%20Oval!5e0!3m2!1sen!2sau!4v1717920206906!5m2!1sen!2sau"
      width="100%"
      height="150"
      style="border:0; display:block;"
      allowfullscreen=""
      loading="lazy"
      referrerpolicy="no-referrer-when-downgrade">
    </iframe>
  </div>
</footer>

<script>
  const footer = document.getElementById('pageFooter');
  function checkScroll() {
    const scrollPosition = window.scrollY + window.innerHeight;
    const nearBottom = document.documentElement.scrollHeight - scrollPosition < 50;
    if (nearBottom) {
      footer.classList.remove('hidden');
    } else {
      footer.classList.add('hidden');
    }
  }
  window.addEventListener('scroll', checkScroll);
  window.addEventListener('resize', checkScroll);
  checkScroll();
</script>

</body>
</html>