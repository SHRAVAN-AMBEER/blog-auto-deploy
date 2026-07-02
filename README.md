# blog-auto-deploy

A personal blog built with Hugo and automated deployment via GitHub Actions to an AWS EC2 instance.


Table of Contents
- About
- Features
- Architecture
- Prerequisites
- Quick start (local)
- Deployment (GitHub Actions → EC2)
- Configuration / Secrets
- Troubleshooting
- Contributing
- License
- Contact

About
This repository contains a Hugo-based personal blog and a GitHub Actions workflow that automatically builds the static site and deploys it to an AWS EC2 host (via SSH/rsync or a similar method).

Features
- Hugo static site generator
- Automatic build + deploy on push to main via GitHub Actions
- Simple EC2-based deployment target (SSH)
- Customize theme, content, and configuration

Architecture
- Source: repo (Hugo project)
- CI: GitHub Actions builds the site
- Host: AWS EC2 instance serving the generated static files (e.g., nginx)

Prerequisites
- Local:
  - Git
  - Hugo (for local development)
- Deployment:
  - AWS account with an EC2 instance (set up nginx or other static server)
  - A user on the EC2 host with SSH access and permission to write the web root
  - GitHub repository secrets configured (see Configuration / Secrets)

Quick start — local development
1. Clone the repository
   git clone https://github.com/SHRAVAN-AMBEER/blog-auto-deploy.git
2. Enter the project and run Hugo server
   cd blog-auto-deploy
   hugo server -D
3. Open http://localhost:1313 to preview

Deployment (GitHub Actions → EC2)
This project uses a GitHub Actions workflow that:
- Installs Hugo
- Builds the static site (`hugo`)
- Copies the generated `public/` directory to your EC2 host (via rsync/ssh) or pushes to the server's web root

Typical workflow steps you should expect:
- Trigger: push to `main` (or on release)
- Build: run `hugo` to create `/public`
- Deploy: use `rsync` or `scp` over SSH to copy files to EC2

Configuration / GitHub Secrets
Add these secrets to your repository (Settings → Secrets):
- EC2_HOST — IP or hostname of your EC2 instance
- EC2_USER — username used to SSH to EC2 (e.g., ubuntu)
- EC2_SSH_PRIVATE_KEY — private key used for SSH (this should be the private half of a keypair that matches a public key in `~/.ssh/authorized_keys` on the EC2 host)
- WEB_ROOT — (optional) path to the web root on the server (e.g., /var/www/html)
- (Optional) ADDITIONAL_SSH_OPTIONS — any special ssh/rsync options

Example GitHub Actions deploy step (concept)
- name: Deploy to EC2
  uses: appleboy/ssh-action@v0.1.8
  with:
    host: ${{ secrets.EC2_HOST }}
    username: ${{ secrets.EC2_USER }}
    key: ${{ secrets.EC2_SSH_PRIVATE_KEY }}
    script: |
      rm -rf $WEB_ROOT/*
      rsync -avz --delete ./public/ ${{ secrets.EC2_USER }}@${{ secrets.EC2_HOST }}:$WEB_ROOT/

(Adjust for your preferred action or custom scripts; store secrets securely.)

Troubleshooting
- Permission denied on SSH: ensure the EC2 instance has your public key in `~/.ssh/authorized_keys`.
- Files not updated: check that the workflow completes successfully and that rsync/scp target path is correct.
- Hugo build fails: ensure Hugo version matches your site’s requirements. Pin required version in the workflow.

Suggestions / Next improvements
- Add a screenshot of the site homepage.
- Add a LICENSE (MIT or similar) and link the badge.
- Add a CONTRIBUTING.md with development guidelines.
- Document the GitHub Actions workflow file (path: .github/workflows/deploy.yml) and list which events trigger deployment.
- Add automated tests (linting for markdown or site links) if needed.
- Provide an architecture diagram or ASCII diagram of CI → EC2 flow.

Contributing
Contributions are welcome. Please open an issue or submit a PR with proposed changes.
