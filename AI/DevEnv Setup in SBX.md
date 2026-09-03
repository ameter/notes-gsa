on the host:

sbx policy allow network "nixos.org,*.nixos.org,*.cachix.org"

in the sandbox:

sudo apt-get install -y xz-utils

Install Nix

curl --proto '=https' --tlsv1.2 -L https://nixos.org/nix/install | sh -s -- --no-daemon

# Write the Nix env into the sandbox's persistent env file (sourced by both
# interactive shells via ~/.bashrc and non-interactive shells via BASH_ENV).
cat >> /etc/sandbox-persistent.sh <<'EOF'

# Nix requires USER to be set; this sandbox leaves it empty in some shells.
\[ -z "${USER-}" \] && export USER="$(id -un)"

# Load the Nix profile (sets PATH, NIX_PROFILES, NIX_SSL_CERT_FILE, etc.)
if \[ -e "$HOME/.nix-profile/etc/profile.d/nix.sh" \]; then
    . "$HOME/.nix-profile/etc/profile.d/nix.sh"
fi
EOF

Reload and verify:

source /etc/sandbox-persistent.sh
nix --version

Notes for your docs:

If /etc/sandbox-persistent.sh is root-owned in a future image, prefix the cat with sudo (use sudo tee -a instead of >>).
This relies on the sandbox's existing ~/.bashrc already sourcing /etc/sandbox-persistent.sh and exporting BASH_ENV=/etc/sandbox-persistent.sh (it does by default here).
The USER guard is the key fix — the official nix.sh skips its PATH export when $USER is empty, which this sandbox triggers.


Install devenv

# for latest unstable version:
# nix-env --install --attr devenv -f https://github.com/NixOS/nixpkgs/tarball/nixpkgs-unstable

# for specific version (e.g. version used by login.gov):
nix-env --install devenv -f https://github.com/NixOS/nixpkgs/archive/refs/tags/25.11.tar.gz


Run devenv for your project/repo

cd <path to your repo>
devenv up -d
devenv shell





bundle exec rspec spec/features/idv/hybrid_mobile/hybrid_socure_mobile_spec.rb:55



chromium-chromedriver
chromium-browser