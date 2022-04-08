```sh
vi Chart.yaml # bump version
rm azure-sql-edge-mock-*.tgz
helm package .
helm repo index . --merge index.yaml --url https://github.com/richminchukio/helm-azure-sql-edge-mock/archive/refs/tags/
# fix tar.gz
rm azure-sql-edge-mock-*.tgz
vi CHANGELOG.md
git add *
git commit -m ""
git tag -f azure-sql-edge-mock-$(yq eval '.version' Chart.yaml)
git push --all
git push -f --tags
helm repo update
```