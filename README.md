Driver Drowsiness Detection System.​ ​
April 08, 2022
Purpose​ of making this project.
​ 

Road accidents have become a matter of concern due to the huge increase in traffic. The main or the primary cause of accidents is due to the drowsiness of drivers during the night time. Fatigue and drowsiness are some of the leading causes of major accidents on Highways.

The only solution to this problem is detecting the drowsiness and alerting the driver.

Tools and technology used.
Open-Cv.
Python language.
Libraries used.
CV2:- For image processing functions.
Dlib:- For deep learning and also for more accuracy of landmarks. 
Numpy:- For array related functions.

Logic of project
The project includes direct working with the 68 facial landmark detector and also the face detector of the Dlib library. The 68 facial landmark detector is a robustly trained efficient detector which detects the points on the human face using which we determine whether the eyes are open or they are closed.




The 68-landmark detector data (.dat) file can be found http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2 

The working of the project
As you can see the above screenshot where the landmarks are detected using the
detector.
Now we are taking the ratio which is described as 'Sum of distances of vertical landmarks divided by twice the distance between horizontal landmarks'.
Now this ratio is totally dependent on your system which you may configure accordingly for the thresholds of sleeping, drowsy, active.





























PYTHON CODE
#Importing OpenCV Library for basic image processing functions

import cv2

# Numpy for array related functions

import numpy as np

# Dlib for deep learning based Modules and face landmark detection

import dlib

#face_utils for basic operations of conversion

from imutils import face_utils

#Initializing the camera and taking the instance

cap = cv2.VideoCapture(0)

#Initializing the face detector and landmark detector

detector = dlib.get_frontal_face_detector()

predictor = dlib.shape_predictor("shape_predictor_68_face_landmarks.dat")

#status marking for current state

sleep = 0

drowsy = 0

active = 0

status=""

color=(0,0,0)

def compute(ptA,ptB):

	dist = np.linalg.norm(ptA - ptB)

	return dist

def blinked(a,b,c,d,e,f):

	up = compute(b,d) + compute(c,e)

	down = compute(a,f)

	ratio = up/(2.0*down)

	#Checking if it is blinked

	if(ratio>0.25):

		return 2

	elif(ratio>0.21 and ratio<=0.25):

		return 1

	else:

		return 0

while True:

    _, frame = cap.read()

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    faces = detector(gray)

    #detected face in faces array

    face_frame = frame.copy()

    for face in faces:

        x1 = face.left()

        y1 = face.top()

        x2 = face.right()

        y2 = face.bottom()

        cv2.rectangle(face_frame, (x1, y1), (x2, y2), (0, 255, 0), 2)

        landmarks = predictor(gray, face)

        landmarks = face_utils.shape_to_np(landmarks)

        #The numbers are actually the landmarks which will show eye

        left_blink = blinked(landmarks[36],landmarks[37], 

        	landmarks[38], landmarks[41], landmarks[40], landmarks[39])

        right_blink = blinked(landmarks[42],landmarks[43], 

        	landmarks[44], landmarks[47], landmarks[46], landmarks[45])

        #Now judge what to do for the eye blinks

        if(left_blink==0 or right_blink==0):

        	sleep+=1

        	drowsy=0

        	active=0

        	if(sleep>6):

        		status="SLEEPING !!!"

        		color = (255,0,0)

        elif(left_blink==1 or right_blink==1):

        	sleep=0

        	active=0

        	drowsy+=1

        	if(drowsy>6):

        		status="Drowsy !"

        		color = (0,0,255)

        else:

        	drowsy=0

        	sleep=0

        	active+=1

        	if(active>6):

        		status="Active :)"

        		color = (0,255,0)

        cv2.putText(frame, status, (100,100), cv2.FONT_HERSHEY_SIMPLEX, 1.2, color,3)

        for n in range(0, 68):

        	(x,y) = landmarks[n]

        	cv2.circle(face_frame, (x, y), 1, (255, 255, 255), -1)

    cv2.imshow("Frame", frame)

    try:

       cv2.imshow("Result of detector", face_frame)

    except NameError:

                    cv2.imshow("frame", image)

    key = cv2.waitKey(1)

    if key == 27:

      	break

