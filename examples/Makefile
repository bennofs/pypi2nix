
SENTRY_BUILD_INPUTS="libxslt libxml2 zlib libffi openssl postgresql freetype libjpeg openjpeg zlib libtiff libwebp tcl lcms2 pkgconfig cmake"
LEKTOR_BUILD_INPUTS="libffi openssl unzip"
PYPI2NIX=../result/bin/pypi2nix
NIX_PATH=nixpkgs=https://github.com/NixOS/nixpkgs-channels/archive/nixpkgs-unstable.tar.gz


# commented out sentry since it doesn't work on travis
all: \
	awscli_and_requests \
	empy \
	lektor \
	rss2email \
	scipy \
	sentry \
	vulnix 

clear: \
	awscli_and_requests-clear \
	empy-clear \
	lektor-clear \
	pypi2nix-clear \
	rss2email-clear \
	scipy-clear \
	sentry-clear \
	vulnix-clear


pypi2nix-clear:
	@rm -f ../result

$(PYPI2NIX): pypi2nix-clear
	@echo "building pypi2nix ..."
	@cd .. && nix-build release.nix -A build.x86_64-linux

sentry: sentry.nix
	@echo "building and testing sentry ..."
	@nix-build sentry.nix -o sentry -A interpreter --show-trace &> /dev/null
	@./sentry/bin/python -c 'import sentry'
	@./sentry/bin/sentry --help
	@nix-shell sentry.nix -A interpreter --run "python -c 'import sentry'" 
	@nix-shell sentry.nix -A interpreter --run "sentry --help"

sentry.nix: sentry.txt $(PYPI2NIX)
	@echo "generating sentry nix expressions ..."
	@$(PYPI2NIX) -v --basename sentry \
		-e "https://github.com/garbas/Pillow/archive/pkg-config.tar.gz#egg=Pillow" \
		-e setuptools-scm \
		-e nose \
		-e pytest-runner \
		-r sentry.txt -E $(SENTRY_BUILD_INPUTS) -I $(NIX_PATH) -V "2.7"

sentry.txt: sentry-clear
	@echo "Sentry" > sentry.txt

sentry-clear:
	@rm -f sentry
	@rm -f sentry.txt
	@rm -f sentry.nix
	@rm -f sentry_generated.nix
	@rm -f sentry_frozen.txt


empy: empy.nix
	@echo "building and testing empy ..."
	@nix-build empy.nix -o empy -A interpreter --show-trace
	@./empy/bin/python -c 'import em'
	@nix-shell empy.nix -A interpreter --run "python -c 'import em'"

empy.nix: empy.txt $(PYPI2NIX)
	@echo "generating empy nix expressions ..."
	@$(PYPI2NIX) -v --basename empy -r empy.txt -I $(NIX_PATH) -V "2.7"

empy.txt: empy-clear
	@echo "empy" > empy.txt

empy-clear:
	@rm -f empy
	@rm -f empy.txt
	@rm -f empy.nix
	@rm -f empy_generated.nix
	@rm -f empy_frozen.txt


lektor: lektor.nix
	@echo "building and testing lektor ..."
	@nix-build lektor.nix -o lektor -A interpreter --show-trace
	@./lektor/bin/python -c 'import lektor'
	@./lektor/bin/lektor --help
	@nix-shell lektor.nix -A interpreter --run "python -c 'import lektor'"
	@nix-shell lektor.nix -A interpreter --run "lektor --help"

lektor.nix: lektor.txt $(PYPI2NIX)
	@echo "generating lektor nix expressions ..."
	@$(PYPI2NIX) -v --basename lektor -r lektor.txt -E $(LEKTOR_BUILD_INPUTS) -I $(NIX_PATH) -V "2.7"

lektor.txt: lektor-clear
	@echo "Lektor" > lektor.txt

lektor-clear:
	@rm -f lektor
	@rm -f lektor.txt
	@rm -f lektor.nix
	@rm -f lektor_generated.nix
	@rm -f lektor_frozen.txt



rss2email: rss2email.nix
	@echo "building and testing rss2email ..."
	@nix-build rss2email.nix -o rss2email -A interpreter --show-trace
	@./rss2email/bin/python -c 'import rss2email'
	@./rss2email/bin/r2e --help
	@nix-build rss2email.nix -A interpreter --run "python -c 'import rss2email'"
	@nix-build rss2email.nix -A interpreter --run "r2e --help"

rss2email.nix: rss2email.txt $(PYPI2NIX)
	@echo "generating rss2email nix expressions ..."
	@$(PYPI2NIX) -v --basename rss2email -r rss2email.txt -V "3.4" -I $(NIX_PATH)

rss2email.txt: rss2email-clear
	@echo "https://github.com/wking/rss2email/archive/master.zip#egg=rss2email" > rss2email.txt

rss2email-clear:
	@rm -f rss2email
	@rm -f rss2email.txt
	@rm -f rss2email.nix
	@rm -f rss2email_generated.nix
	@rm -f rss2email_frozen.txt



awscli_and_requests: awscli_and_requests.nix
	@echo "building and testing awscli and requests library ..."
	@nix-build awscli.nix -o awscli_and_requests -A interpreter --show-trace
	@./awscli_and_requests/bin/python -c 'import awscli; import requests'
	@PAGER=none ./awscli_and_requests/bin/aws help
	@nix-shell awscli.nix -A interpreter --run "python -c 'import awscli'"
	@nix-shell awscli.nix -A interpreter --run "python -c 'import awscli; import requests'"
	@nix-shell awscli.nix -A interpreter --run "PAGER=none aws help"

awscli_and_requests.nix: awscli_and_requests.txt $(PYPI2NIX)
	@echo "generating awscli and requests nix expressions ..."
	@$(PYPI2NIX) -v --basename awscli -r awscli.txt -r requests.txt -I $(NIX_PATH) -V "2.7"

awscli_and_requests.txt: awscli_and_requests-clear
	@echo "awscli" > awscli.txt
	@echo "requests" > requests.txt

awscli_and_requests-clear:
	@rm -f awscli_and_requests
	@rm -f awscli.txt
	@rm -f requests.txt
	@rm -f awscli_and_requests.nix
	@rm -f awscli_and_requests_generated.nix
	@rm -f awscli_and_requests_frozen.txt



vulnix: vulnix-clear $(PYPI2NIX)
	@echo "building and testing vulnix..."
	@git clone https://github.com/garbas/vulnix vulnix-src
	@cd vulnix-src && ./../$(PYPI2NIX) -V 3.5 -b buildout.cfg -e pytest-runner -e setuptools-scm -v -I $(NIX_PATH)
	@nix-build vulnix-src/default.nix -o vulnix
	@./vulnix/bin/vulnix --help

vulnix-clear:
	@rm -f vulnix
	@rm -rf vulnix-src


scipy: scipy.nix
	@echo "building and testing scipy..."
	@nix-build scipy.nix -o scipy-A interpreter --show-trace
	@./scipy/bin/python -c 'import scipy'
	@nix-shell scipy.nix -A interpreter --run "python -c 'import scipy'"

scipy.nix: scipy.txt $(PYPI2NIX)
	@echo "generating scipy.nix expressions ..."
	@$(PYPI2NIX) -v --basename scipy -r scipy.txt -s numpy -E gfortran -E blas -I $(NIX_PATH) -V "3.5"

scipy.txt: scipy-clear
	@echo "scipy" > scipy.txt

scipy-clear:
	@rm -f scipy
	@rm -f scipy.txt
	@rm -f scipy.nix
	@rm -f scipy_generated.nix
	@rm -f scipy_frozen.txt
