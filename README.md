# Assignment-2-Documentation
Documentation file containing the codes and infomation used in the creation of my website
# Newman Junior Soccer Association Website#

**Live Site**: [https://newmanjuniorsoccerassociation.click](https://newmanjuniorsoccerassociation.click)

This guide documents the full setup of the NJSA website hosted on an AWS EC2 instance.
---

## Table of Contents
1. EC2 Apache Web Server Setup
2. EC2 SSH Access
3. Installed Apache Web Server 
4. Set the correct document root and enabled SSL
5. Generated and installed a SSL certificate from SSLForFree (obtain a free 90-day SSL certificate)
6. Configured Apache
7. Tested that apache code worked and restared it
8. Editing the 3 Web Pages - Home page, About page, Contact page


---
# EC2 Apache Web Server Setup#
launching an Amazon EC2 instance:
Created AWS account, then logged in log in and went to EC2 dashboard to launch a new instance, I choose the options listed below:
- **AMI**: Ubuntu 20.04 LTS (Free Tier)
- **Instance Type**: t2.micro (Free Tier Eligible)
- **Storage**: Leave default (8 GB)
- **Security Group**: Name it `ssh-and-web`
- **Add rule command**: HTTP 

then i created a key pair:
My SSH key `AWSwebserver-key.pem` is located on my desktop.


# EC2 SSH Access #

      cd Desktop

      ls -la AWSwebserver-key.pem

      chmod 400 AWSwebserver-key.pem

Connect to the EC2 instance via SSH:
      !ssh -i "AWSwebserver-key.pem" ubuntu@ec2-16-176-9-20.ap-southeast-2.compute.amazonaws.com

# Installed Apache Web Server #
      sudo apt update
      sudo apt install apache2

# Set the correct document root and enabled SSL #
      sudo nano /etc/apache2/sites-enabled/default-ssl.conf

Change DocumentRoot to:
      DocumentRoot /var/www/html/

      sudo systemctl reload apache2

      sudo a2enmod ssl

      sudo systemctl restart apache2

# Generated and installed a SSL certificate from SSLForFree (obtain a free 90-day SSL certificate) #
      /etc/ssl/certificate.crt 
      /etc/ssl/private.key
      /etc/ssl/ca_bundle.crt

gave correct permissions to files:
      sudo chmod 600 /etc/ssl/private.key
      sudo chown root:root /etc/ssl/*

 # Configured Apache #
 Then I directed Apache to where to find and use the certificate files. additnoally i used a port 443, which is a HTTPS port.
      <VirtualHost *:443>
   
this code provides the location of where the files are stored to apache:
      DocumentRoot /var/www/html/

this code allows acess to the website via www. or wothout:
      ServerName www.newmanjuniorsoccerassociation.click
      ServerAlias newmanjuniorsoccerassociation.click

    SSLEngine on
Path to your certificate file (from the CA),private key file and CA bundle for testing certificates 
trustworthiness.
      SSLCertificateFile /etc/ssl/certificate.crt
      SSLCertificateKeyFile /etc/ssl/private.key
      SSLCertificateChainFile /etc/ssl/ca_bundle.crt

      </VirtualHost>

# Tested that apache code worked and restared it#
      sudo apache2ctl configtest
    sudo systemctl restart apache2

I then ensured that my domain's name servers match Route 53. previously,the domain registrar's name server records did not match the authoritative name server records in AWS Route 53 so i had to manually change them:
      whois newmanjuniorsoccerassociation.click | grep "Name Server"

results demosted below matched Route 53 on AWS:

      ns-80.awsdns-10.com  
      ns-1925.awsdns-48.co.uk  
      ns-1092.awsdns-08.org  
      ns-798.awsdns-35.net

# Editing the 3 Web Pages#
# HOME PAGE:
      sudo nano /var/www/html/index.html

This code allowed me to create hyper links to the 2 other seperate pages; About page and Contact Page.
        <nav>
        <a href="index.html">Home</a>
        <a href="about.html">About</a>
        <a href="contact.html">Contact</a>
       </nav>

This code highlights the linked page buttons as you hover over them:
        nav a:hover {
        background-color: #ddd;
        color: black;
        }

This code was used on all three pages to genrate the logo wtih the header (after it was uploded and hosted on github):
        <header>
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/278396679_5405959209436215_9109305474040067837_n.jpg?raw=true" alt="NJSA Logo" class="logo">
        <h1>Newman Junior Soccer Association</h1>
        </header>

This code loaded an image hosted on GitHub into the middle of the Home page (full size) adding in round corners to the image:
        <section id="home">
        <h2>Welcome to NJSA!</h2>
        <p>Building community through youth soccer in Newman.</p>
        <img src="https://github.com/Chaos687/njsa-assets/blob/main/434082376_908573474401029_8717107320456803714_n.png?raw=true" alt="NJSA Banner" style="width:100%; border-radius: 10px; margin-top: 10px;">
        </section>

This code gernated the footer for all three pages(Home, About, and Contact page) with the intergated google maps at the exact NJSA location. it also contains the JavaScript for scroll detection:
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
        box-shadow: 0 2px 8px rgba(0,0,0,0.3);
        ">
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
        // Near bottom - show footer - (JavaScript) - Controls visibility of footer
        footer.classList.remove('hidden');
       } else {
        // Not near bottom - hide footer - (JavaScript)- Controls visibility of footer
        footer.classList.add('hidden');
        }
       }
        window.addEventListener('scroll', checkScroll); // - (JavaScript) Detects how far you've scrolled
        window.addEventListener('resize', checkScroll); // - (JavaScript) Detects how far you've scrolled
        // Initial check
        checkScroll(); // - (JavaScript) Detects how far you've scrolled
        </script>

        </body>
       </html>

Below is the code for the Home page (index.html) documented in its entirety:
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>About Us - Newman Junior Soccer Association</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
    }

    /* Corrected header styles using Flexbox */
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
      float: left;color: white;
      text-align: center;
      padding: 14px 16px;
      text-decoration: none;
    }

    nav a:hover {
      background-color: #ddd;
      color: black;
    }

    section {
      padding: 20px;
    }

    footer {
      background-color: #4CAF50;
      color: white;
      text-align: center;
      padding: 10px;
      position: fixed;
      width: 100%;
      bottom: 0;
    }
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
      <img src="https://github.com/Chaos687/njsa-assets/blob/main/434082376_908573474401029_8717107320456803714_n.png?raw=true" alt="NJSA Banner" style="width:100%; border-radius: 10px; margin-top: 10px;"> </section> 

  
<style>
  body {
    padding-bottom: 180px; /* space for the fixed footer height */
    margin: 0; /* optional, to remove default margin */
    box-sizing: border-box;
  }footer {
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

    /* Add transition for smooth hide/show */
    transition: transform 0.3s ease;
    transform: translateY(0); /* visible by default */
  }

  /* Hide footer by moving it down */
  footer.hidden {
    transform: translateY(100%);
  }
</style>

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
    </div></div>
  <div style="
    flex: 0 1 300px;
    max-width: 300px;
    border-radius: 15px;
    overflow: hidden;
    box-shadow: 0 2px 8px rgba(0,0,0,0.3);
  ">
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
      // Near bottom - show footer
      footer.classList.remove('hidden');
    } else {
      // Not near bottom - hide footer
      footer.classList.add('hidden');
    }
  }

  window.addEventListener('scroll', checkScroll);
  window.addEventListener('resize', checkScroll);

  // Initial check
  checkScroll();
</script>

</body>
</html>


# ABOUT PAGE:
      sudo nano /var/www/html/about.html

This code gerated the carocel of images; these images move in a slide horizontally from right to left, with the images repeating twice to create a seamless loop (when the first set of images scrolls out of view):
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
        0% {
        transform: translateX(0%);
        }
        100% {
        transform: translateX(-50%);
        }
       }
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

Below is the code for the About page (about.html) documented in its entirety:
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>About Us - Newman Junior Soccer Association</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
    }

    /* Corrected header styles using Flexbox */
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
  0% {
    transform: translateX(0%);
  }
  100% {
    transform: translateX(-50%);
  }
}
    nav a:hover {
      background-color: #ddd;
      color: black;
    }

    section {
      padding: 20px;
    }

    footer {
      background-color: #4CAF50;
      color: white;
      text-align: center;
      padding: 10px;
      position: fixed;
      width: 100%;
      bottom: 0;
    }
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
    <p>Newman Junior Soccer Association (NJSA) is a long-standing junior sporting association located in Newman, a regional area within Western Australia with a residential population of 6,761 (ABS Census Data, 2021).
 Due to the isolated nature of living in remote areas, sporting groups like NJSA is essential to increase liveability,
 create a sense of community, promote healthy living through physical activity and continue to provide development pathways for peak-performing athletes.</p>
  </section>

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

<style>
  body {
    padding-bottom: 180px; /* space for the fixed footer height */
    margin: 0; /* optional, to remove default margin */
    box-sizing: border-box;
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

    /* Add transition for smooth hide/show */
    transition: transform 0.3s ease;
    transform: translateY(0); /* visible by default */
  }

  /* Hide footer by moving it down */
  footer.hidden {
    transform: translateY(100%);
  }
</style>

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
    box-shadow: 0 2px 8px rgba(0,0,0,0.3);
  ">
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
      // Near bottom - show footer
      footer.classList.remove('hidden');
    } else {
      // Not near bottom - hide footer
      footer.classList.add('hidden');
    }
  }

  window.addEventListener('scroll', checkScroll);
  window.addEventListener('resize', checkScroll);

  // Initial check
  checkScroll();
</script>

</body>
</html>


# CONTACT PAGE:
      sudo nano /var/www/html/contact.html

This code below generates the “Contact Us” form (does not actually work when used, but can enter text into text boxes) and the contact info section containg mutiple fake emails and names of fictional individuals working for NJSA:
<style>
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
    padding: 10px; /* Adds space inside fields */
    margin-bottom: 15px; 
    border: 1px solid #ccc; /* Light gray border */
    border-radius: 4px; /* Rounded corners */
    font-size: 14px; 
  }

  textarea {
    height: 100px; 
  }

  button {
    padding: 10px 20px; 
    background-color: #5ca145; /* Green button color */
    color: white; /* White text */
    border: none; 
    border-radius: 4px; 
    cursor: pointer; 
  }

  .contact-info h3 {
    margin-bottom: 10px; /* Space below the "Contacts" subheading */
  }

  .contact-row {
    display: flex; /* Puts name, role, and email in one row */
    justify-content: space-between; 
    border-bottom: 1px solid #ccc; 
    padding: 6px 0; 
    font-size: 14px; 
    flex-wrap: wrap; 
  }

  .contact-info a {
    color: green; /* Makes the email addresses green */
    text-decoration: none; /* Removes underline from links */
  }

  .contact-info a:hover {
    text-decoration: underline; /* Underline email on hover */
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
</style>
<!-- Contact section begins -->
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
    <!-- Contact section begins -->
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

<!-- Sponsor logos section --> (this section of code is irrelevant to anything visable on the website and should be ignored if repoducing the site)
<div class="logos">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/58/Dot_painting_example.svg/1200px-Dot_painting_example.svg.png" alt="KingKira Logo">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Breight.png/600px-Breight.png" alt="Breight Logo">
</div>

Below is the code for the Contact page (contact.html) documented in its entirety:
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
    }button {
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
      z-index: 1000;font-family: Arial, sans-serif;
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
</section><div class="logos">
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
<iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3236.0039405795337!2d119.73335161526105!3d-23.36169418075262!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x2b93b4e6bb0a0f67%3A0xe2cac18a51c587d8!2sCapricorn%20Oval!5e0!3m2!1sen!2sau!4v1716825301440!5m2!1sen!2sau"width="100%"
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
