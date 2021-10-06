name: POI RELEASE

on:
  workflow_dispatch:
  push:
    branches: 
    - main
    paths:
    - 'apis/poi/**'
    - '.github/workflows/poi-release.yml'

env:
  APPLICATIONPATH: apis/poi/
  APPLICATIONNAME: openhackqug903g3poi
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}/poi
  
jobs:
  build-and-push-image:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Log in to the Container registry
        uses: docker/login-action@f054a8b539a109f9f41c372932f1ae047eff08c9
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@ad44023a93711e3deb337508980b4b5e9bcdc5dc
        with:
          context: ${{ env.APPLICATIONPATH }}/web
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.run_number }}

  deploy:
    needs: build-and-push-image
    runs-on: ubuntu-latest
    steps:
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    #- uses: azure/docker-login@v1
      #with:
      #  login-server: mycontainer.azurecr.io
      #  username: ${{ secrets.REGISTRY_USERNAME }}
      #  password: ${{ secrets.REGISTRY_PASSWORD }}
    #- run: |
    #    docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
    #    docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.APPLICATIONNAME }}
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.run_number }}
    
    - name: Azure logout
      run: |
        az logout
