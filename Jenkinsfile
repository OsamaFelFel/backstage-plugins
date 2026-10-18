// The bootstrap supplies the isolated rootless BuildKit pod and source checkout.
stage('Build portal and push') {
  timeout(time: 60, unit: 'MINUTES') {
    container('buildkit') {
      sh '''#!/bin/sh
set -eu
set +x
buildctl-daemonless.sh build --frontend dockerfile.v0 \
  --local context=. --local dockerfile=. \
  --opt filename=packages/backend/Dockerfile \
  --output "type=image,name=$REGISTRY_HOST/openchoreo-portal:$SOURCE_COMMIT,push=true" \
  --metadata-file build-metadata.json
'''
    }
    archiveArtifacts(artifacts: 'build-metadata.json')
  }
}
