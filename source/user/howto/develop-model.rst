.. highlight:: console

**************************************
Develop a model using DEEP DS template
**************************************


1. Prepare DEEP DS environment
------------------------------


Install cookiecutter (if not yet done)
::

	$ pip install cookiecutter

Run the DEEP DS cookiecutter template
::

	$ cookiecutter https://github.com/indigo-dc/cookiecutter-data-science

Answer all questions from DEEP DS cookiecutter template with attentions to
``repo_name`` i.e. the name of your github repositories, etc.
This creates two project directories:
::

	~/DEEP-OC-your_project
	~/your_project

Go to ``github.com/your_account`` and
create corresponding repositories: ``DEEP-OC-your_project`` and ``your_project``
Do ``git push origin master`` in both created directories. This puts your initial code to ``github``.


2. Improve the initial code of the model
----------------------------------------

The structure of ``your_project`` created using
`DEEP DS template <https://github.com/indigo-dc/cookiecutter-data-science>`_ contains
the following core items needed to develop a DEEP DS model:
::

	requirements.txt
	data/
	models/
	{{repo_name}}/dataset/make_dataset.py
	{{repo_name}}/features/build_features.py
	{{repo_name}}/models/model.py


2.1 Installing development requirements

Modify ``requirements.txt`` according to your needs (e.g. add more libraries) then run
::

	$ pip install -r requirements.txt

You can modify and add more ``source files`` and put them
accordingly into the directory structure.


2.2 Make datasets
=================

Source files in this directory aim to manipulate raw datasets.
The output of this step is also raw data, but cleaned and/or pre-formatted.
::

	{{repo_name}}/dataset/make_dataset.py
	{{repo_name}}/dataset/


2.3 Build features
===================

This step takes the output from the previous step `Make datasets` and
creates train, test as well as validation ML data from raw but cleaned and pre-formatted data.
The realisation of this step depends on the concrete use case, the aim of the application as well as
available technological backgrounds (e.g. high-performance supports for data processing).
::

	{{repo_name}}/features/build_features.py
	{{repo_name}}/features/


2.4 Develop models
==================

This step deals with the most interesting phase in ML i.e. modelling.
The most important thing of DEEP DS models is located in ``model.py``
containing DEEP entry point implementations.
DEEP entry points are defined using :ref:`API methods <user/overview/api:Methods>`.
You don't need to implement all of them, just the ones you need.
::

	{{repo_name}}/models/model.py
	{{repo_name}}/models/


3. Create a docker containe for your model
=========================================

Once your model is well in place, you can encapsulate it by creating a docker container. For this you need to create a Dockerfile. This file will contain the information about the Docker, including the type of operating system you want to run on and the packages you need installed to make your package run.

The simplest Dockerfile could look like this::

	FROM ubuntu:18.04

	WORKDIR /srv

	#Download and install your model package
	RUN git clone https://github.com/your_git/your_model_package && \
    	cd image-classification-tf && \
    	python -m pip install -e . && \
	cd ..

	#Install DEEPaaS
	pip install deepaas

	# Install rclone
	RUN wget https://downloads.rclone.org/rclone-current-linux-amd64.deb && \
    	dpkg -i rclone-current-linux-amd64.deb && \
    	apt install -f && \
    	rm rclone-current-linux-amd64.deb && \
    	apt-get clean && \
    	rm -rf /var/lib/apt/lists/* && \
    	rm -rf /root/.cache/pip/* && \
    	rm -rf /tmp/*

	# Expose API on port 5000 and tensorboard on port 6006
	EXPOSE 5000 6006

    	CMD deepaas-run --listen-ip 0.0.0.0


For more details on rclone or on DEEPaas API you can check :doc:`here <rclone>` and `here <https://github.com/indigo-dc/DEEPaaS>`_ respectively.

If you want to see an example of a more complex Dockerfile, you can check it `here <https://github.com/deephdc/DEEP-OC-image-classification-tf/blob/master/Dockerfile>`_.

In order to compile the Dockerfile, you should choose a name for the container and use the docker build command::

	docker build -t your_container_name -f Dockerfile


You can then upload it to Docker hub so that you can download the already compiled image directly. To do so, follow the instructions `here <https://docs.docker.com/docker-hub/repos/>`_.

