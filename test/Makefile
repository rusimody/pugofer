# Makefile for Pug testing environment and execution
# Should be run from the 'test' directory, or via 'make -C test <target>' from root.

# Determine the Python executable to use.
# Prefers the one in ../.pug-test if it exists, otherwise defaults to python3.
# This path is relative to where python commands will be run (project root after 'cd ..')
VENV_PYTHON := .pug-test/bin/python3
SYSTEM_PYTHON := python3
PYTHON_TO_USE := $(shell if [ -f ../$(VENV_PYTHON) ]; then echo $(VENV_PYTHON); else echo $(SYSTEM_PYTHON); fi)

.PHONY: all setup run reports ci clean

all: run

setup:
	@if ! command -v uv > /dev/null; then \
		echo "Error: uv command not found. Please install uv first."; \
		echo "See https://astral.sh/uv for installation instructions."; \
		exit 1; \
	fi
	@echo "Creating Python 3.12 virtual environment in ../.pug-test using uv..."
	cd .. && uv venv .pug-test --python 3.12
	@echo "Installing test dependencies from ../requirements-test.txt..."
	cd .. && uv pip install --python .pug-test/bin/python3 -r requirements-test.txt
	@echo ""
	@echo "Test environment setup complete."
	@echo "To activate the virtual environment (from project root), run: source .pug-test/bin/activate"

run:
	@echo "Running tests from project root using $(PYTHON_TO_USE)..."
	cd .. && $(PYTHON_TO_USE) -m unittest discover -v

reports:
	@echo "Running tests with reports from project root using $(PYTHON_TO_USE)..."
	cd .. && $(PYTHON_TO_USE) test/run_tests_with_reports.py --verbose --timeout 180

ci:
	@echo "Running CI tests with reports from project root using $(PYTHON_TO_USE)..."
	cd .. && $(PYTHON_TO_USE) test/run_tests_with_reports.py --verbose --xml-output test-reports --timeout 180

clean:
	@echo "Removing Python virtual environment and test artifacts from project root..."
	rm -rf ../.pug-test
	@echo "Removing __pycache__ directories..."
	# Remove __pycache__ from project root if it exists
	@if [ -d ../__pycache__ ]; then rm -rf ../__pycache__; fi
	# Remove __pycache__ from test directory if it exists
	@if [ -d ./__pycache__ ]; then rm -rf ./__pycache__; fi
	@echo "Removing coverage and report files/directories..."
	rm -f ../.coverage
	rm -rf ../htmlcov/
	rm -rf ../test-reports/
	@echo "Test environment cleanup complete."
