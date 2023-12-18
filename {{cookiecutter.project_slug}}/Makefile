VENV = .venv
PYTHON = $(VENV)/bin/python3 #this is only for unix
PIP = $(VENV)/bin/pip #this is only for unix


help:
	@awk 'BEGIN {FS = ":.*##"; printf "\033[35m\033[0m"} /^[a-zA-Z_-]+:.*?##/ { printf "  \033[36m%-20s\033[0m %s\n", $$1, $$2 } /^##@/ { printf "\n\033[1m%s\033[0m\n", substr($$1, 5) } ' $(MAKEFILE_LIST)

##@ Docker

docker-build: ##builds the docker container
	@docker build -t {{cookiecutter.short_project_name}}-python .

docker-jup: ##creates the jupyter-lab instance
	@docker run -d --name jupyter-lab-{{cookiecutter.short_project_name}} \
	-v $(CURDIR)/notebooks:/project/notebooks -v $(CURDIR)/src:/project/src \
	-p 8888:8888 {{cookiecutter.short_project_name}}-python jupyter lab
	@docker stop jupyter-lab-{{cookiecutter.short_project_name}}

docker-dash: ##creates the dash app instance
	@docker run -w /project/data_app -it --name dash-app \
	-v $(CURDIR)/data_app:/project/data_app -v $(CURDIR)/src:/project/src \
	-p 8888:8888 {{cookiecutter.short_project_name}}-python python3 model_page.py
	@docker stop dash-app

#TODO try to automatically install all the neccessary r packages
docker-rstudio: ##creates the rstudio docker instance
	@docker build -f Dockerfile-rstudio -t new-rstudio .
	@docker run -d --name rstudio-{{cookiecutter.short_project_name}} \
	-v $(CURDIR)/notebooks:/home/project/notebooks -v $(CURDIR)/src:/home/project/src \
	-e PASSWORD=password -p 8787:8787 new-rstudio
	@docker stop rstudio-{{cookiecutter.short_project_name}}

docker-clean: ##removes all instances of the docker containers
	@docker rm dash-app jupyter-lab-{{cookiecutter.short_project_name}} rstudio-{{cookiecutter.short_project_name}}


##@ Jupyter Lab
launch-jup: ##launches the jupyter lab instance
	@docker start jupyter-lab-{{cookiecutter.short_project_name}}
	@echo "Go to http://localhost:8888 to access the jupyter notebook"

stop-jup: ##stops the jupyter lab instance
	@docker stop jupyter-lab-{{cookiecutter.short_project_name}}
	@echo "The jupyter lab instance has been stopped"

bash-jup: ##runs bash inside the jupyter docker container
	@docker exec -it jupyter-lab-{{cookiecutter.short_project_name}} bash

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
	@docker start rstudio-{{cookiecutter.short_project_name}}
	@echo "Go to http://localhost:8787"
	@echo "Use 'rstudio' as a username and 'password' as the password"

stop-rstudio: ##stops RStudio
	@docker stop rstudio-{{cookiecutter.short_project_name}}

bash-rstudio: ##runs bash within the rstudio container
	@docker exec -it rstudio-{{cookiecutter.short_project_name}} bash

##@ Local Python Environment
create-env: ##creates the local python environment
	@python3 -m venv .venv
	@.venv/bin/python3 -m pip install --upgrade pip
	@.venv/bin/python3 -m pip install -r docker/requirements.txt

delete-env: ##deletes the current local python environment
	@rm -r .venv

