# CGRE FRAMEWORK

A Framework to create a dataset and use it to evaluate the Tesseract OCR software for the usecase on webpages.
You can use it to evaluate other OCR software.

Pre:
    Docker:
        
        alias ocr='docker run ocr '
        alias ocrp='docker run ocr pipenv run python'
        https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket
    pyenv:
        ``https://github.com/pyenv/pyenv/wiki``
        (sudo apt-get update; sudo apt-get install --no-install-recommends make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev)
        ``Im Zweifel: pipenv --rm``
        ``env PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.7.2``
        ``pyenv local 3.7.2``
        ``pip install -U setuptools``


build docker image:
`` docker build -t ocr . ``

start docker container (pipenv shell will be started automatically):
`` docker run -v AbsolutePathToSomeDir:/data:rw -ti ocr ``

example call to create dataset:
`` python dataset/creation/main.py -o /data/results -t 3 -v -b ``

main.py

    -i:
        a file containing one url per line to use for crawling (not needed when crawling is skipped)
    -o:
        path to an output folder        
    -t:
        provide a number indicating on which entry to stop when parsing the crawl data (i.e. '-t 3' the 3 most used stylings per attribute will be parsed)
        Default:
            1
    -s:
        enter a string indicating which step you want to skip, if you're string contains:
        'c' crawling will be skipped and the program assumes crawl data in '-c'
        'g' html generating will be skipped and the program assumes html data in '-g'
        'r' html rendering will be skipped and the program assumes rendered data in '-r'
    -c:
        provide a path to where the crawl data file will be saved (in output folder)
        Default:
            'html'
    -g:
        provide a path to where the html data file will be saved (in output folder)
        Default:
            'crawl.json'
    -r:
        provide a path to where the rendered data file will be saved (in output folder)
        Default:
            'dataset'
    -b:
        adds boxes to the rendered data, saved in output folder with the '-r' + '_boxes'
    -v:
        creates visualisations for the crawled data
    -z:
        zips the output folder




generate:
`` pipenv run python generate_html.py ``
    => './html/font_family/font_size/font_style/layout.html'

render ( & save ):
`` pipenv run python render_html.py ``

    => './dataset/font_family/font_size/font_style/layout.png'  
    => './dataset/font_family/font_size/font_style/layout.txt'  
        (contains words and their boxes in this format: // word\t(left,top,width,height)\n)  
        (first line contains path to the corresponding html file)

zip:
`` pipenv run python zip_dataset.py ``

    => zips the 'dataset' directory with the same structure to 'dataset.zip'

evaluation:
`` pipenv run python evaluate_combinations.py ideal recognized -o output_folder``

    => evaluates the recognized dataset against the ideal using different IoU and Levenshtein Thresholds -> 0.5 to 0.9 respectively
        True Positives 
        False Positives
        False Negatives
        Accuracy (for localisation)
        Precision (for localisation and determination)
        Recall (for localisation)
    => they need to have the same structure
    => results in 50 files:
        'evaluation_ideal_recognized_cp05_lp05.csv'
        'evaluation_ideal_recognized_cp05_lp05.txt'
        'evaluation_ideal_recognized_cp05_lp06.csv'
        'evaluation_ideal_recognized_cp05_lp06.txt'
        ...
        'evaluation_ideal_recognized_cp09_lp09.csv'
        'evaluation_ideal_recognized_cp09_lp09.txt'
        
        *.csv files contain time measurements and TP/FP/FN for every single webdocument
        *.txt files contain  Accuracy Precision Recall and TP/FP/FN for every webdocument accumulated
reset virtual env:
``pipenv --rm``
