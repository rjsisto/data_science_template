VENV = .venv
PYTHON = $(VENV)/bin/python3 #this is only for unix
PIP = $(VENV)/bin/pip #this is only for unix
LOG_FILE=$(CURDIR)/docker/logs/prev_reqs.txt
JUPYTER_LAB = jupyter-lab-{{cookiecutter.short_project_name}}
R_STUDIO = rstudio-{{cookiecutter.short_project_name}}
PYTHON_IMAGE = {{cookiecutter.short_project_name}}-python

help:
	@awk 'BEGIN {FS = ":.*##"; printf "\033[35m\033[0m"} /^[a-zA-Z_-]+:.*?##/ { printf "  \033[36m%-20s\033[0m %s\n", $$1, $$2 } /^##@/ { printf "\n\033[1m%s\033[0m\n", substr($$1, 5) } ' $(MAKEFILE_LIST)

##@ Docker

docker-build: ##builds the docker container
	@docker build -t $(PYTHON_IMAGE) .

docker-jup: ##creates the jupyter-lab instance
	@docker run -d --name $(JUPYTER_LAB) \
	-v $(CURDIR)/notebooks:/project/notebooks -v $(CURDIR)/src:/project/src \
	-p 8888:8888 $(PYTHON_IMAGE) jupyter lab
	@docker stop $(JUPYTER_LAB)

docker-dash: ##creates the dash app instance
	@docker run -w /project/data_app -it --name dash-app \
	-v $(CURDIR)/data_app:/project/data_app -v $(CURDIR)/src:/project/src \
	-p 8888:8888 $(PYTHON_IMAGE) python3 model_page.py
	@docker stop dash-app

#TODO try to automatically install all the neccessary r packages
docker-rstudio: ##creates the rstudio docker instance
	@docker build -f Dockerfile-rstudio -t new-rstudio .
	@docker run -d --name $(R_STUDIO) \
	-v $(CURDIR)/notebooks:/home/project/notebooks -v $(CURDIR)/src:/home/project/src \
	-e PASSWORD=password -p 8787:8787 new-rstudio
	@docker stop $(R_STUDIO)

docker-clean: ##removes all instances of the docker containers
	@bash docker/get_reqs.sh $(LOG_FILE) $(JUPYTER_LAB) $(R_STUDIO)
	@docker rm dash-app $(JUPYTER_LAB) $(R_STUDIO)


##@ Jupyter Lab
launch-jup: ##launches the jupyter lab instance
	@docker start $(JUPYTER_LAB)
	@echo "Go to http://localhost:8888 to access the jupyter notebook"

stop-jup: ##stops the jupyter lab instance
	@docker stop $(JUPYTER_LAB)
	@echo "The jupyter lab instance has been stopped"

bash-jup: ##runs bash inside the jupyter docker container
	@docker exec -it $(JUPYTER_LAB) bash

##@ Dash App
#TODO probably dont need launch and stop dash commands
#TODO bash-dash is not working
launch-dash: ##launches the dash app
	@docker start dash-app
	@echo "Go to http://localhost:8888 to access the dash app"

stop-dash: ##stops the dash app
	@docker stop dash-app
	@echo "Dash App has been stopped"

bash-dash: ##runs bash inside the dash-app docker container
	@docker exec -it dash-app bash

##@ RStudio
launch-rstudio: ##launches RStudio
	@docker start $(R_STUDIO)
	@echo "Go to http://localhost:8787"
	@echo "Use 'rstudio' as a username and 'password' as the password"

stop-rstudio: ##stops RStudio
	@docker stop $(R_STUDIO)

bash-rstudio: ##runs bash within the rstudio container
	@docker exec -it $(R_STUDIO) bash

##@ Local Python Environment
create-env: ##creates the local python environment
	@python3 -m venv .venv
	@.venv/bin/python3 -m pip install --upgrade pip
	@.venv/bin/python3 -m pip install -r docker/requirements.txt

delete-env: ##deletes the current local python environment
	@rm -r .venv

