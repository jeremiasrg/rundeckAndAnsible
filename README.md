# Rundeck + Ansible + devs (2) machines + elasticsearch + kibana

This project was created to improve knowledge in Rundeck, Ansible, Ansible Playbooks syntax, and more.

### Get started

```bash
docker compose up -d
```

This command will create:

- 1 container with Rundeck and Ansible.
- 1 container with Elasticksearch
- 1 container with Kibana
- 2 containers with Ubuntu distro, simulating two development machines
  - To make it easy, both containers are configured with SSH (User: admin, Pass: Admin)
- 1 network called **myCompanyNetwork.**

### Shared Config files

In this project, you can see some config files. These config files are shared with containers using volume configuration.

This approach was created to avoid lost time. You can change all the configurations using your Vscode and the changes will reflect inside of the container.

Find below the list of shared files in this project:

**Ansible**

---

- ./ansible-config/ansible.cfg
- ./ansible-config/hosts

**Elasticsearch**

---

- ./elasticsearch/elasticsearch.yml

**Kibana**

---

- ./kibana/kibana.yml

**Rundeck**

---

- ./rundeck-config/framework.properties
- ./rundeck-config/project.properties
- ./rundeck-config/rundeck-config.properties

### Access

Rundeck

```bash
localhost:4440/
```

Kibana:

```bash
localhost:5601
```

### Docker Compose:

Feel free to change de default "infrastructure" below to your POC or study case.

```bash
version: "3"
services:
  rundeck-ansible:
    image: jeremiasrg/rundeck-ansible:1.0.0
    container_name: rundeck-ansible
    volumes:
      - "./rundeck-config/framework.properties:/etc/rundeck/framework.properties:ro"
      - "./rundeck-config/project.properties:/etc/rundeck/project.properties:ro"
      - "./rundeck-config/rundeck-config.properties:/etc/rundeck/rundeck-config.properties:ro"
      - "./rundeck-config/resources.xml:/resources.xml:ro"
      - "./ansible-config/ansible.cfg:/etc/ansible/ansible.cfg:ro"
      - "./ansible-config/hosts:/etc/ansible/hosts:ro"
    ports:
      - 4440:4440
    networks:
      - myCompanyNetwork

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.9.2
    container_name: elasticsearch
    ports:
      - 9200:9200
      - 9300:9300
    volumes:
      - ./elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
    environment:
      discovery.type: single-node
      ELASTIC_USERNAME: elastic
      ELASTIC_PASSWORD: elastic
    networks:
      - myCompanyNetwork

  kibana:
    image: docker.elastic.co/kibana/kibana:7.9.2
    container_name: kibana
    ports:
      - 5601:5601
    volumes:
      - ./kibana/kibana.yml:/usr/share/kibana/config/kibana.yml
    environment:
      ELASTICSEARCH_URL: http://elasticsearch:9200
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    networks:
      - myCompanyNetwork

  dev01:
    image: jeremiasrg/ubuntu-ssh:1.0.0
    container_name: dev01
    networks:
      - myCompanyNetwork

  dev02:
    image: jeremiasrg/ubuntu-ssh:1.0.0
    container_name: dev02
    networks:
      - myCompanyNetwork

networks:
  myCompanyNetwork:
    driver: bridge
```

If it helped you, try to help someone else too. ;)
