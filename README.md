# Create Your Debian Package

A simple tutorial to create and install your very own Debian package.

## Pre-requisite

Ensure you have a Debian-based operating system such as Debian, Ubuntu, or a similar derivative installed.

## Architecture and Notes

This guide will demonstrate how to package a sample Django web application (_samplesite_) into a Debian package for distribution.

### Package Structure

The package, named `samplewebapp_1.0_all`, will install and serve a Django application using Gunicorn. The folder structure for the package is as follows:

```plaintext
.
├── debian
│   ├── control                # Package metadata
│   ├── postinst               # Script executed after installation
│   ├── preinst                # Script executed before installation
│   ├── rules                  # Build instructions
│   └── samplewebapp.service   # Systemd service configuration
├── samplesite                 # Django project folder
│   ├── app                    # Django application
│   ├── manage.py              # Django management script
│   ├── requirements.txt       # Dependencies
│   ├── settings.py            # Project settings
│   └── wsgi.py                # WSGI entry point
└── README.md
```

### Key Components in `debian/control`

The `control` file contains metadata about the package:

```conf
Package: samplewebapp
Version: 1.0
Architecture: all
Essential: no
Priority: optional
Depends: python3, python3-pip, python3-venv, gunicorn
Maintainer: Your Name <your.email@example.com>
Description: A sample Django web application packaged as a Debian package.
```

###  if you want to change the port please do it on samplesize.init
```
BIND=0.0.0.0:8080
```

## Building the Debian Package

1. Navigate to the package directory:
   ```bash
   cd /path/to/samplesite
   ```

2. Build the Debian package:
   ```bash
   dpkg-buildpackage -us -uc
   ```
   This will generate a `.deb` package in the parent directory.

## Installing the Package

1. Install the package using `dpkg`:
   ```bash
   sudo dpkg -i ../samplewebapp_1.0_all.deb
   ```

2. If dependencies are missing, install them using:
   ```bash
   sudo apt --fix-broken install
   ```

3. To uninstall the package:
   ```bash
   sudo apt autoremove samplewebapp
   ```

## Deploying the Application without .deb

### Using Gunicorn and systemd

1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

2. Start the application using systemd:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable samplewebapp.service
   sudo systemctl start samplewebapp.service
   ```

3. Check the status of the service:
   ```bash
   sudo systemctl status samplewebapp.service
   ```

## Accessing the Application

Visit the following URL in your browser to access the application:
#### [http://0.0.0.0:8000](http://0.0.0.0:8000)

## Advanced: Hosting Your Package in a Local Repository

1. Create a folder for the repository:
   ```bash
   mkdir -p /path/to/mirror/pool
   cp ../samplewebapp_1.0_all.deb /path/to/mirror/pool/
   ```

2. Create a `Packages` index:
   ```bash
   cd /path/to/mirror
   dpkg-scanpackages -m ./pool > Packages
   ```

3. Add the repository to your sources list:
   ```bash
   echo "deb [trusted=yes] file:/path/to/mirror /" | sudo tee /etc/apt/sources.list.d/samplewebapp.list
   ```

4. Update your package list and install:
   ```bash
   sudo apt update
   sudo apt install samplewebapp
   ```

## Notes on APT vs Snap

- APT is the traditional package management system for Debian-based distributions and uses `.deb` packages.
- Snap, introduced by Canonical, provides cross-distribution compatibility and includes dependencies in each package. While Snap packages are larger, they offer improved sandboxing and automatic updates.

## License

This project is licensed under the Apache 2.0

