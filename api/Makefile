MODULE = api

ENVDIR = ./env
ACTIVATE = $(ENVDIR)/bin/activate
COVERAGE = $(ENVDIR)/bin/coverage
NOSE = $(ENVDIR)/bin/nosetests
PEP257 = $(ENVDIR)/bin/pep257
PEP8 = $(ENVDIR)/bin/pep8
PIP = C_INCLUDE_PATH="/opt/local/include:/usr/local/include" $(ENVDIR)/bin/pip
PIPOPTS = -r requirements.txt
PYLINT = $(ENVDIR)/bin/pylint
PYTHON = $(ENVDIR)/bin/python
PYTHON_VERSION = python2.7
REPORTDIR = reports
VIRTUALENV = virtualenv
VIRTUALENVOPTS = --python=$(PYTHON_VERSION)

## Testing ##
.PHONY: test coverage

test: unit-test integration-test
coverage: unit-coverage

%-test: $(REPORTDIR)
	@echo Running $* tests
	$(DEVELOPMENT_ENV) $(NOSE) --cover-package=$(MODULE),tests --tests=tests/$* --with-xunit --xunit-file=$(REPORTDIR)/$*-xunit.xml

%-coverage: %-test
	@echo Generating $* coverage reports
	$(COVERAGE) html -d $(REPORTDIR)/htmlcov-$* --omit=$(ENVDIR)/*
	$(COVERAGE) xml  -o $(REPORTDIR)/$*-coverage.xml --omit=$(ENVDIR)/*

$(REPORTDIR): requirements
	test -d "$@" || mkdir -p "$@"
	touch "$@"

## Static Analysis ##
.PHONY: lint pep257 pep8 pylint
lint: pep257 pep8 pylint

pylint: $(REPORTDIR)
	$(PYLINT) --reports=y --output-format=parseable --rcfile=pylintrc $(MODULE) | tee $(REPORTDIR)/$(MODULE)_lint.txt

pep8: $(REPORTDIR)
	$(PEP8) --filename="*.py" --repeat $(MODULE) | tee $(REPORTDIR)/pep8.txt

pep257:
	$(PEP257) $(MODULE) 2>&1 | egrep -v '0: (First line should end with a period|Blank line missing after one-line summary)'

## Local Setup ##
.PHONY: requirements req virtualenv
requirements:
	@rm -f .req
	$(MAKE) .req

req: .req
.req: $(ENVDIR) requirements.txt
	$(PIP) install $(PIPOPTS)
	@touch .req

virtualenv: $(ENVDIR)
$(ENVDIR):
	$(VIRTUALENV) $(VIRTUALENVOPTS) $(ENVDIR)
