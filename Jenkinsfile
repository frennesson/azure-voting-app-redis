node {
    stage('Echo on Master') {
        if (env.BRANCH_NAME == 'main') {
            echo 'This is the main branch'
        } else {
            echo 'This is NOT the main branch'
        }
    }
}