
#include<simplecpp>

bool isRightContactCircle(int xi, int yi, int ri, double* ly){
    if (yi>=ly[xi+ri])  return 1;
    else return 0;
    }

bool isDownContactCircle(int xi,int yi, int ri, double*ly){
   if ((yi+ri)>=ly[xi]) return 1;
   else return 0;
   }

bool isTank2ContactCircle(int xi, int yi, int ri, int cx2, int cy2, int r) {
    if (((xi - cx2)*(xi-cx2) + (yi-cy2)*(yi-cy2)) <= (ri+r)*(ri+r) ) return 1;
    else return 0;
    }

bool isTank1ContactCircle(int xi, int yi, int ri, int cx1, int cy1, int r) {
    if (((xi - cx1)*(xi-cx1) + (yi-cy1)*(yi-cy1)) <= (ri+r)*(ri+r) ) return 1;
    else return 0;
    }

void singleshot1(int &xb1, int &yb1, int &xb2, int&yb2 ,int wb1, int a1, int &cx2, int&cy2, int &cx1, int &cy1, int r,
                double *ly, Line **l, Circle &tank1, Circle &tank2,Rectangle &barrel1, Rectangle &barrel2, int &score1, int w, int power1){
    int damagesingleshot =60,damagesnipershot=100,damagecruiser=40, damagemissile = 35, damagescatter = 20;

    int xi, yi, ri = r/3.0;

    xi = xb1 + wb1*cosine(a1) + ri;
    yi = yb1 - wb1*sine(a1) - ri;
    Circle sshot (xi, yi, ri);

    bool tank = isTank2ContactCircle(xi, yi, ri, cx2, cy2, r);
    bool down = isDownContactCircle(xi, yi, ri, ly);
    bool right = isRightContactCircle(xi, yi, ri, ly);

    double gravity = 0.1;
    int vx = ((power1)/5)*cosine(a1);
    double vy=-((power1/5.0))*sine(a1);

    bool didchange = 0;

    while (!tank && !down && !right && xi<895 && xi>5){
                xi +=vx;
                yi +=vy;
                sshot.moveTo(xi, yi);
                vy +=gravity;
                wait(0.02);

                tank = isTank2ContactCircle(xi, yi, ri, cx2, cy2, r);
                down = isDownContactCircle(xi, yi, ri, ly);


            if (w==6 & xi>=460 & didchange ==0){
                vy = 1.0*vx*(cy2-yi)/(cx2-xi);
                gravity = 0;
                didchange =1;
                }
            }


    if (tank){
        sshot.hide();
        if(w==1){
            score1+=damagesingleshot;
            tank2.setColor(COLOR(0,0,256));
            cout << "score 1 is " << score1;
            wait(2);
            }

        else if(w==2){
            score1+=damagesnipershot;
            cout << "score 1 is " << score1;
            tank2.setColor(COLOR(256,0,0));
            repeat(6){
                if (cx2<870 && cx2>30){
                    cx2+=5;
                    cy2 = ly[cx2] - r;
                    xb2=cx2-r;
                    yb2=cy2-2.0*r/5;
                    tank2.moveTo(cx2, ly[cx2]-r);
                    barrel2.moveTo(xb2,yb2);
                    wait(0.02);}
                else break;
                }
            }

        else if(w==3){
            score1+=damagecruiser;
            tank2.setColor(COLOR(256,0,256));
            cout << "score 1 is " << score1;
            wait(2);
            }

        else if (w==6){
            score1 +=damagemissile;
            cout << "score 1 is " << score1;
            wait(2);
            }

        else if (w==7){
            score1+= damagescatter*3;
            cout << "score 1 is " << score1;
            }

            tank2.setColor(COLOR(0,0,0));
            }

    else if (down){

        if(w==1 || w==6){
            sshot.hide();
            for (int i = -25 ; i <=25; i++ ){
                if((xi+i)<900 && (xi+i)>0){
                    int drop = pow(25*25 - i*i, 0.5);
                    ly[xi+i]+=drop;
                    /*int a = ly[xi+i], b=ly[xi]+drop;

                    if(b>=a){
                    ly[xi+i]=b;
                    int change = b-a;*/
                    l[xi+i]->move(0,drop);
                    wait(0.02);
                    }
                }

            cy2 = ly[cx2] - r;
            xb2=cx2-r;
            yb2=cy2-2.0*r/5;

            cy1 = ly[cx1] - r;
            xb1=cx1+r;
            yb1=cy1-2.0*r/5;

            tank1.moveTo(cx1, ly[cx1]-r);
            barrel1.moveTo(xb1, yb1);
            tank2.moveTo(cx2, ly[cx2]-r);
            barrel2.moveTo(xb2,yb2);

            }

        else if(w==2){
            sshot.hide();
            }

        else if(w==3){
            yi = ly[xi]-ri;
            sshot.moveTo(xi,yi);
            repeat(30){
                if(!tank){
                    xi+=5;
                    yi = ly[xi]-ri;
                    sshot.moveTo(xi,yi);
                    tank = isTank2ContactCircle(xi, yi, ri, cx2, cy2, r);
                wait(0.02);
                }
                    if(tank){
                        sshot.hide();
                        score1+=damagecruiser;
                        cout << "score 1 is " << score1;
                        tank2.setColor(COLOR(256,0,256));
                        wait(2);
                        break;
                        }
                    }
                }

            else if (w==7){
                sshot.hide();

                int ri1 = r/6.0;

                int xi1[3];
                for (int i =0; i<3 ; i++) xi1[i]= xi;

                int yi1[3];
                for(int i=0 ; i<3 ; i++) yi1[i]=yi;

                Circle *shot[3];
                for (int i= 0 ; i<3 ; i++)  shot[i] = new Circle(xi1[i], yi1[i], ri1);

                bool tank1[3];
                for(int i =0 ; i<3 ; i++)  tank1[i] = isTank2ContactCircle(xi1[i], yi1[i], ri1, cx2, cy2, r);

                bool down1[3];
                for (int i=0 ; i<3 ; i++) down1[i] = isDownContactCircle(xi1[i], yi1[i], ri1, ly);

                bool canshot[3];
                for (int i=0 ; i<3 ; i++) canshot[i]=1;

                int vx1[3];
                for (int i=0; i<3 ; i++) vx1[i] = ((15)/5)*cosine(60+30*i);

                double vy1[3];
                for (int i=0 ; i<3 ; i++) vy1[i] = -(15.0/5)*sine(60+30*i);

                while(canshot[0] || canshot[1] || canshot[2]){
                    for (int i =0 ; i<3 ; i++){
                        if(canshot[i]){
                            if (xi1[i]< 895){
                                xi1[i] +=vx1[i];
                                yi1[i] +=vy1[i];
                                shot[i]->moveTo(xi1[i], yi1[i]);

                                vy1[i] +=gravity;
                                wait(0.02);

                                tank1[i] = isTank2ContactCircle(xi1[i], yi1[i], ri1, cx2, cy2, r);
                                down1[i] = isDownContactCircle(xi1[i], yi1[i], ri1, ly);
                                }

                        if(down1 [i]){
                            canshot[i] = 0;

                            shot[i]->hide();

                            for (int j = -15 ; j <=15; j++ ){
                                if((xi1[i]+j)<900 && (xi1[i]+j)>0){
                                    int drop = pow(15*15 - j*j, 0.5);
                                    ly[xi1[i]+j]+=drop;
                                    l[xi1[i]+j]->move(0,drop);
                                    wait(0.02);
                                    }
                                }

                            }

                            if (tank1[i]){
                                canshot[i]=0;
                                shot[i]->moveTo(1000,1000);
                                score1+=damagescatter;
                                wait(2);
                                cout << "score 1 is " << score1;
                                }
                            }
                        }
                    }
                }
        }

    //turn=1;
    }

void singleshot2(int &xb2, int &yb2, int &xb1, int&yb1 ,int wb2, int a2, int &cx1, int&cy1, int &cx2, int &cy2, int r,
                double *ly, Line **l, Circle &tank2, Circle &tank1,Rectangle &barrel2, Rectangle &barrel1, int &score2, int w, int power2){
    int damagesingleshot =60,damagesnipershot=100,damagecruiser=40, damagemissile = 35, damagescatter = 20;

    int xi, yi, ri = r/3.0;

    xi = xb2 - wb2*cosine(a2) - ri;
    yi = yb2 - wb2*sine(a2) - ri;
    Circle sshot (xi, yi, ri);

    bool tank = isTank1ContactCircle(xi, yi, ri, cx1, cy1, r);
    bool down = isDownContactCircle(xi, yi, ri, ly);
    bool right = isRightContactCircle(xi, yi, ri, ly);

    double gravity = 0.1;
    int vx = -((power2)/5)*cosine(a2);
    double vy=-((power2/5))*sine(a2);

    bool didchange = 0;

    while (!tank && !down && !right && xi<895 && xi>5){

                xi +=vx;
                yi +=vy;
                sshot.moveTo(xi, yi);
                vy +=gravity;
                wait(0.02);


                tank = isTank1ContactCircle(xi, yi, ri, cx1, cy1, r);
                down = isDownContactCircle(xi, yi, ri, ly);

            if (w==6 & xi<=460 & didchange ==0){
                vy = -1.0*vx*(cy1-yi)/(cx1-xi);
                gravity = 0;
                didchange =1;
                }
            }


    if (tank){
        sshot.hide();
        if(w==1){
            score2+=damagesingleshot;
            tank1.setColor(COLOR(0,0,256));
            cout << "score 1 is " << score2;
            wait(2);
            }

        else if(w==2){
            score2+=damagesnipershot;
            cout << "score 1 is " << score2;
            tank1.setColor(COLOR(256,0,0));
            repeat(6){
                if (cx1<870 && cx1>30){
                    cx1-=5;
                    cy1 = ly[cx1] - r;
                    xb1=cx1+r;
                    yb1=cy1-2.0*r/5;
                    tank1.moveTo(cx1, ly[cx1]-r);
                    barrel1.moveTo(xb1,yb1);
                    wait(0.02);
                    }
                else break;
                }
            }

            else if(w==3){
                score2+=damagecruiser;
                tank1.setColor(COLOR(256,0,256));
                cout << "score 1 is " << score2;
                wait(2);
                }

            else if (w==6){
                score2 +=damagemissile;
                cout << "score 1 is " << score2;
                wait(2);
                }

            else if (w==7){
                score2+= damagescatter*3;
                cout << "score 1 is " << score2;
                }

                tank1.setColor(COLOR(0,0,0));
            }

    else if (down){

        if(w==1 || w==6){
            sshot.hide();
            for (int i = -25 ; i <=25; i++ ){
                if((xi+i)<900 && (xi+i)>0){
                    int drop = pow(25*25 - i*i, 0.5);
                    ly[xi+i]+=drop;
                    l[xi+i]->move(0,drop);
                    wait(0.02);
                    }
                }

            cy1 = ly[cx1] - r;
            xb1=cx1+r;
            yb1=cy1-2.0*r/5;

            cy2 = ly[cx2] - r;
            xb2=cx2-r;
            yb2=cy2-2.0*r/5;

            tank2.moveTo(cx2, ly[cx2]-r);
            barrel2.moveTo(xb2, yb2);
            tank1.moveTo(cx1, ly[cx1]-r);
            barrel1.moveTo(xb1,yb1);

            }

        else if(w==2){
            cout << "you missed!" << endl;
            sshot.hide();
            }

        else if(w==3){
            yi = ly[xi]-ri;
            sshot.moveTo(xi,yi);
            repeat(30){
                if(!tank){
                    xi-=5;
                    yi = ly[xi]-ri;
                    sshot.moveTo(xi,yi);
                    tank = isTank1ContactCircle(xi, yi, ri, cx1, cy1, r);
                    wait(0.02);
                    }

                    if(tank){
                        sshot.hide();
                        score2+=damagecruiser;
                        tank1.setColor(COLOR(256,0,256));
                        wait(2);
                        break;
                        }
                    }
                }

            else if (w==7){
                sshot.hide();

                int ri1 = r/6.0;

                int xi1[3];
                for (int i =0; i<3 ; i++) xi1[i]= xi;

                int yi1[3];
                for(int i=0 ; i<3 ; i++) yi1[i]=yi;

                Circle *shot[3];
                for (int i= 0 ; i<3 ; i++)  shot[i] = new Circle(xi1[i], yi1[i], ri1);

                bool tank2[3];
                for(int i =0 ; i<3 ; i++)  tank2[i] = isTank1ContactCircle(xi1[i], yi1[i], ri1, cx1, cy1, r);

                bool down2[3];
                for (int i=0 ; i<3 ; i++) down2[i] = isDownContactCircle(xi1[i], yi1[i], ri1, ly);

                bool canshot[3];
                for (int i=0 ; i<3 ; i++) canshot[i]=1;

                int vx1[3];
                for (int i=0; i<3 ; i++) vx1[i] = ((15)/5)*cosine(60+30*i);

                double vy1[3];
                for (int i=0 ; i<3 ; i++) vy1[i] = -(15.0/5)*sine(60+30*i);

                while(canshot[0] || canshot[1] || canshot[2]){
                    for (int i =0 ; i<3 ; i++){
                        if(canshot[i]){
                            if (xi1[i]< 895 && xi1[i]>5){
                                xi1[i] +=vx1[i];
                                yi1[i] +=vy1[i];
                                shot[i]->moveTo(xi1[i], yi1[i]);

                                vy1[i] +=gravity;

                                tank2[i] = isTank1ContactCircle(xi1[i], yi1[i], ri1, cx1, cy1, r);
                                down2[i] = isDownContactCircle(xi1[i], yi1[i], ri1, ly);
                                wait(0.02);
                                }

                        if(down2 [i]){
                            canshot[i] = 0;

                            shot[i]->hide();

                            for (int j = -15 ; j <=15; j++ ){
                                if((xi1[i]+j)<900 && (xi1[i]+j)>0){
                                    int drop = pow(15*15 - j*j, 0.5);
                                    ly[xi1[i]+j]+=drop;
                                    l[xi1[i]+j]->move(0,drop);
                                    wait(0.02);
                                    }
                                }

                            }

                            if (tank2[i]){
                                canshot[i]=0;
                                shot[i]->moveTo(1000,1000);
                                score2+=damagescatter;
                                wait(2);
                                cout << "score 1 is " << score2;
                                }
                            }
                        }
                    }
                }
        }


    }



void threeshot1(int &xb1, int &yb1, int &xb2, int&yb2 ,int wb1, int a1, int &cx2, int&cy2, int &cx1, int &cy1, int r,
                double *ly, Line **l, Circle &tank1, Circle &tank2,Rectangle &barrel1, Rectangle &barrel2, int &score1, int w, int power1){
    int damagethreeshot = 30;

    int ri = r/3.0;

    int xi[3];
    for (int i =0; i<3 ; i++) xi[i]= xb1 + wb1*cosine(a1) + ri;

    int yi[3];
    for(int i=0 ; i<3 ; i++) yi[i]=yb1 - wb1*sine(a1) + ri;

    Circle *shot[3];
    for (int i= 0 ; i<3 ; i++)  shot[i] = new Circle(xi[i], yi[i], ri);

    bool tank[3];
    for(int i =0 ; i<3 ; i++)  tank[i] = isTank2ContactCircle(xi[i], yi[i], ri, cx2, cy2, r);

    bool down[3];
    for (int i=0 ; i<3 ; i++) down[i] = isDownContactCircle(xi[i], yi[i], ri, ly);

    bool canshot[3];
    for (int i=0 ; i<3 ; i++) canshot[i]=1;

    double gravity[3];
    gravity [0] = 0.1;
    gravity [1] = 0.2;
    gravity [2] = 0.3;

    int vx[3];
    for (int i=0; i<3 ; i++) vx[i] = ((power1)/5)*cosine(a1);

    double vy[3];
    for (int i=0 ; i<3 ; i++) vy[i] = -((power1/5))*sine(a1);

    while(canshot[0] || canshot[1] || canshot[2]){
        for (int i =0 ; i<3 ; i++){
            if(canshot[i]){
                if (xi[i]< 890){
                    xi[i] +=vx[i];
                    yi[i] +=vy[i];
                    shot[i]->moveTo(xi[i], yi[i]);
                    wait(0.02);

                    vy[i] +=gravity[i];

                    tank[i] = isTank2ContactCircle(xi[i], yi[i], ri, cx2, cy2, r);
                    down[i] = isDownContactCircle(xi[i], yi[i], ri, ly);
                    }

                if(down [i]){
                    canshot[i] = 0;

                    shot[i]->hide();

                    for (int j = -15 ; j <=15; j++ ){
                        if((xi[i]+j)<900 && (xi[i]+j)>0){
                        int drop = pow(15*15 - j*j, 0.5);
                        ly[xi[i]+j]+=drop;
                        l[xi[i]+j]->move(0,drop);
                        wait(0.02);
                        }
                    }

                    cy2 = ly[cx2] - r;
                    xb2=cx2-r;
                    yb2=cy2-2.0*r/5;

                    cy1 = ly[cx1] - r;
                    xb1=cx1+r;
                    yb1=cy1-2.0*r/5;

                    tank1.moveTo(cx1, ly[cx1]-r);
                    barrel1.moveTo(xb1, yb1);
                    tank2.moveTo(cx2, ly[cx2]-r);
                    barrel2.moveTo(xb2,yb2);
                    }

                if (tank[i]){
                    canshot[i]=0;
                    shot[i]->moveTo(1000,1000);
                    score1+=damagethreeshot;
                    wait(2);
                    cout << "score 1 is " << score1;
                    }
                if (xi[i]>=890) canshot[i]=0;
                }
            }
        }


    }

void threeshot2(int &xb2, int &yb2, int &xb1, int&yb1 ,int wb2, int a2, int &cx1, int&cy1, int &cx2, int &cy2, int r,
                double *ly, Line **l, Circle &tank2, Circle &tank1,Rectangle &barrel2, Rectangle &barrel1, int &score2, int w, int power2){
    int damagethreeshot = 30;
    int power;

    int ri = r/3.0;

    int xi[3];
    for (int i =0; i<3 ; i++) xi[i]= xb2 - wb2*cosine(a2) - ri;

    int yi[3];
    for(int i=0 ; i<3 ; i++) yi[i]=yb2 - wb2*sine(a2) - ri;

    Circle *shot[3];
    for (int i= 0 ; i<3 ; i++)  shot[i] = new Circle(xi[i], yi[i], ri);

    bool tank[3];
    for(int i =0 ; i<3 ; i++)  tank[i] = isTank1ContactCircle(xi[i], yi[i], ri, cx1, cy1, r);

    bool down[3];
    for (int i=0 ; i<3 ; i++) down[i] = isDownContactCircle(xi[i], yi[i], ri, ly);

    bool canshot[3];
    for (int i=0 ; i<3 ; i++) canshot[i]=1;

    double gravity[3];
    gravity [0] = 0.1;
    gravity [1] = 0.2;
    gravity [2] = 0.3;

    int vx[3];
    for (int i=0; i<3 ; i++) vx[i] = -((power2)/5)*cosine(a2);

    double vy[3];
    for (int i=0 ; i<3 ; i++) vy[i] = -((power2/5))*sine(a2);

    while(canshot[0] || canshot[1] || canshot[2]){
        for (int i =0 ; i<3 ; i++){
            if(canshot[i]){
                if (xi[i]< 890){
                    xi[i] +=vx[i];
                    yi[i] +=vy[i];
                    shot[i]->moveTo(xi[i], yi[i]);

                    vy[i] +=gravity[i];
                    wait(0.02);

                    tank[i] = isTank1ContactCircle(xi[i], yi[i], ri, cx1, cy1, r);
                    down[i] = isDownContactCircle(xi[i], yi[i], ri, ly);
                    }

                if(down [i]){
                    canshot[i] = 0;

                    shot[i]->hide();

                    for (int j = -15 ; j <=15; j++ ){
                        if((xi[i]+j)<900 && (xi[i]+j)>0){
                        int drop = pow(15*15 - j*j, 0.5);
                        ly[xi[i]+j]+=drop;
                        l[xi[i]+j]->move(0,drop);
                        wait(0.02);
                        }
                    }

                    cy1 = ly[cx1] - r;
                    xb1=cx1+r;
                    yb1=cy1-2.0*r/5;

                    cy2 = ly[cx2] - r;
                    xb2=cx2-r;
                    yb2=cy2-2.0*r/5;

                    tank2.moveTo(cx2, ly[cx2]-r);
                    barrel2.moveTo(xb2, yb2);
                    tank1.moveTo(cx1, ly[cx1]-r);
                    barrel1.moveTo(xb1,yb1);
                    }

                if (tank[i]){
                    canshot[i]=0;
                    shot[i]->moveTo(1000,1000);
                    score2+=damagethreeshot;
                    wait(2);
                    cout << "score 2 is " << score2 << endl;
                    }
                if (xi[i]>=890) canshot[i]=0;
                }
            }
        }


    }


void laser1(int &xb1, int &yb1, int &xb2, int&yb2 ,int wb1, int a1, int &cx2, int&cy2, int &cx1, int &cy1, int r,
                double *ly, Line **l, Circle &tank1, Circle &tank2,Rectangle &barrel1, Rectangle &barrel2, int &score1, int w, int power1){
    int damagelaser=30;

    int xi, yi, ri = r/8.0;

    xi = xb1 + wb1*cosine(a1) + ri;
    yi = yb1 - wb1*sine(a1) + ri;
    Circle laser (xi, yi, ri);


    bool tank = isTank2ContactCircle(xi, yi, ri, cx2, cy2, r);
    bool down = isDownContactCircle(xi, yi, ri, ly);
    bool right = isRightContactCircle(xi, yi, ri, ly);

    int vx = ((power1)/5)*cosine(a1);
    double vy=-((power1/5))*sine(a1);

    int minp=900, maxp=0;

    while(!tank && xi<890 && xi>10){
        xi+=vx;
        yi+=vy;
        laser.moveTo(xi,yi);
        wait(0.02);
        down = isDownContactCircle(xi, yi, ri, ly);
        tank = isTank2ContactCircle(xi, yi, ri, cx2, cy2, r);
        if(down){
            if(xi<minp) minp=xi;
            if(xi>maxp) maxp = xi;
            }

        }
        Line eraseg(minp, ly[minp], maxp, ly[maxp]);
    if(tank){
        score1+=damagelaser;
        cout << "score 1 is " << score1;
        }

    for(int i=minp ; i<maxp ; i++){
        ly[i]+=2*ri;
        l[i]->move(0,2*ri);
        }

    cy2 = ly[cx2] - r;
    xb2=cx2-r;
    yb2=cy2-2.0*r/5;

    cy1 = ly[cx1] - r;
    xb1=cx1+r;
    yb1=cy1-2.0*r/5;

    tank1.moveTo(cx1, ly[cx1]-r);
    barrel1.moveTo(xb1, yb1);
    tank2.moveTo(cx2, ly[cx2]-r);
    barrel2.moveTo(xb2,yb2);


    }

void laser2(int &xb2, int &yb2, int &xb1, int&yb1 ,int wb2, int a2, int &cx1, int&cy1, int &cx2, int &cy2, int r,
                double *ly, Line **l, Circle &tank2, Circle &tank1,Rectangle &barrel2, Rectangle &barrel1, int &score2, int w, int power2){
    int damagelaser=30;

    int xi, yi, ri = r/8.0;

    xi = xb2 - wb2*cosine(a2) - ri;
    yi = yb2 - wb2*sine(a2) - ri;
    Circle laser (xi, yi, ri);


    bool tank = isTank1ContactCircle(xi, yi, ri, cx1, cy1, r);
    bool down = isDownContactCircle(xi, yi, ri, ly);
    bool right = isRightContactCircle(xi, yi, ri, ly);

    int vx = -((power2)/5)*cosine(a2);
    double vy=-((power2/5))*sine(a2);

    int minp=900, maxp=0;

    while(!tank && xi<890 && xi>10){
        xi+=vx;
        yi+=vy;
        laser.moveTo(xi,yi);
        wait(0.02);

        down = isDownContactCircle(xi, yi, ri, ly);
        tank = isTank1ContactCircle(xi, yi, ri, cx1, cy1, r);
        if(down){
            if(xi<minp) minp=xi;
            if(xi>maxp) maxp = xi;
            }


        }
        Line eraseg(minp, ly[minp], maxp, ly[maxp]);
    if(tank){
        score2+=damagelaser;
        cout << "score 2 is " << score2;
        }

    for(int i=minp ; i<maxp ; i++){
        ly[i]+=2*ri;
        l[i]->move(0,2*ri);
        }

    cy1 = ly[cx1] - r;
    xb1=cx1+r;
    yb1=cy1-2.0*r/5;

    cy2 = ly[cx2] - r;
    xb2=cx2-r;
    yb2=cy2-2.0*r/5;

    tank2.moveTo(cx2, ly[cx2]-r);
    barrel2.moveTo(xb2, yb2);
    tank1.moveTo(cx1, ly[cx1]-r);
    barrel1.moveTo(xb1,yb1);


    }

void moveWeapon(int &xi, int &yi, int vx, int &vy, Circle &sshot, int gravity){
    xi +=vx;
    yi +=vy;
    sshot.moveTo(xi, yi);
    vy +=gravity;
    }


void moveTank1(char moveTank, int &cx1, int &cy1, double *ly, int &xb1, int &yb1, int r, Circle &tank1, Rectangle &barrel1, int &a1){
    repeat(5){

    if (moveTank=='M'){
        if(cx1<400){
            cx1= cx1+5;
            cy1 = ly[cx1]-r;
            xb1=cx1+r;
            yb1=cy1-2.0*r/5;
            tank1.moveTo(cx1, cy1);
            barrel1.moveTo(xb1, yb1);
            }
        }

    if (moveTank=='K'){
        if(cx1>20){
            cx1= cx1-5;
            cy1= ly[cx1]-r;
            xb1=cx1+r;
            yb1=cy1-2.0*r/5;
            tank1.moveTo(cx1,cy1);
            barrel1.moveTo(xb1,yb1);
            }
        }
    }

    if (moveTank=='H'){
        float angle=-25.0/60;
        if (a1<95){
            a1+=25;
            //barrel1.move(-5.0*r/90,0);
            barrel1.rotate(angle);
            }
        }
    if (moveTank == 'P'){
        if(a1>-5){
            float angle=25.0/60;
            a1-=25;
            //barrel1.move(5.0*r/90,0);
            barrel1.rotate(angle);
            }
        }

    }

void moveTank2(char moveTank, int &cx2, int &cy2, double *ly, int &xb2, int &yb2, int r, Circle &tank2, Rectangle &barrel2, int &a2){
    repeat(5){
    if (moveTank=='M'){
        if(cx2 < 880){
            cx2= cx2+5;
            cy2 = ly[cx2]-r;
            xb2=cx2-r;
            yb2=cy2-2.0*r/5;
            tank2.moveTo(cx2, cy2);
                    barrel2.moveTo(xb2, yb2);
            }
        }

    if (moveTank=='K'){
        if (cx2>500){
            cx2= cx2-5;
            cy2= ly[cx2]-r;

            xb2=cx2-r;
            yb2=cy2-2.0*r/5;

            tank2.moveTo(cx2,cy2);
            barrel2.moveTo(xb2,yb2);
            }
        }

    }

    if (moveTank=='H'){
        float angle=25.0/60;
        if (a2<95){
            a2+=25;
            //barrel2.move(5.0*r/90.0,0);
            barrel2.rotate(angle);
            }
        }
    if (moveTank == 'P'){
        if(a2>-5){
            float angle=-25.0/60;
            a2-=25;
            //barrel2.move(-5.0*r/90.0,0);
            barrel2.rotate(angle);
            }
        }

    }
main_program{
    initCanvas("POCKET TANKS",900,600);
    int r;
    Rectangle RSurround(450,300,900,600);

    for(int i=1;i<300;i++){
        Circle  c1(450,300,r);
        r=i;
        c1.setColor(COLOR(238,0,0));
        c1.setFill();
        wait(0.02);
        }
    RSurround.setColor(COLOR(0,0,0));
    RSurround.setFill();
    wait(3);
    textWidth(100);
    RSurround.hide();
    Text Tmakers(450,300,"MADE BY: MEET , SHYAM , ABU , ANKIT");
    wait(2);
    Tmakers.hide();
    RSurround.show();
    RSurround.setColor(COLOR(0 ,    191 ,    255 ));
    RSurround.setFill();
    Text PTanks(450,200,"POCKET TANKS");
    wait(1);
    Text C1(450,300,"Click anywhere to continue");
    int clickval=getClick();//always put this inside the repeat
    if (clickval/65536<=900&&clickval/65536>=0&&clickval%65536>=0&&clickval%65536<=600){
        C1.hide();
        PTanks.hide();
        RSurround.hide();
        Rectangle bkgrd(450,350,900,700);
        bkgrd.setFill();
        bkgrd.setColor(COLOR(127 , 255 , 0));
        Circle play(450,180,75);
        play.setFill();
        play.setColor(COLOR(255,255,255));
        Text playgame(450,180,"PLAY GAME");
        playgame.setFill();
        playgame.setColor(COLOR(0,150,0));
        Rectangle inst(450,365,200,100);
        inst.setFill();
        inst.setColor(COLOR(255,255,255));
        Text instr(450,365,"INSTRUCTIONS");
        instr.setFill();
        instr.setColor(COLOR(0,0,255));
        Circle c3(350,365,50);
        c3.setFill();
        c3.setColor(COLOR(255,255,255));
        Circle c4(550,365,50);
        c4.setFill();
        c4.setColor(COLOR(255,255,255));
        Rectangle exit(450,550,200,100);
        exit.setFill();
        exit.setColor(COLOR(255,255,255));
        Text exits(450,550,"EXIT");
        exits.setFill();
        exits.setColor(COLOR(255,0,0));

        while(true){
            int clickPos=getClick();
            int cx=clickPos/65536;
            int cy=clickPos%65536;

            if(cx>380&&cx<520&&cy>175&&cy<240)                                   //This if statement is for Play Game
                {
                playgame.hide();
                instr.hide();
                exits.hide();
                bkgrd.hide();
                play.hide();
                inst.hide();
                exit.hide();
                Rectangle gm(450,300,1000,1000);
                gm.setColor(COLOR(176 ,226 ,255 ));
                gm.setFill();
                Line *l[900];
                double lx[900],ly[900];
                for (int i=0 ; i < 150 ; i++){
                    lx[i]=i; ly[i]=400+(150-i)*(150-i)/225;
                    }

                for (int i=150 ; i < 250 ; i++){
                    lx[i]=i; ly[i]=350+5*sqrt(250-i);
                    }

                for (int i=250 ; i <= 350 ; i++){
                    lx[i]=i; ly[i]=350-5*sqrt(i-250);
                    }

                for (int i=350; i <= 450 ; i++){
                    lx[i]=i; ly[i]=300-100*sin((i-350)*3.14/200);                                             //ly[i-1]+rand()%10-5
                    }

                for (int i=450; i <= 550 ; i++){
                    lx[i]=i; ly[i]=ly[900-i];
                    }

                for (int i=550; i <= 900 ; i++){
                    lx[i]=i; ly[i]=500-3600.0/7+4.0/7*i;
                    }


                for (int i=0; i<900; i++){
                    l[i]= new Line (lx[i],ly[i], lx[i],ly[i]+800);
                    l[i]->setColor(COLOR(0,102,0));
                    }
                int cx1=75;
                int r=25;
                int cy1 = ly[cx1]-r;
                Circle tank1 (cx1, cy1, r);
                tank1.setFill(true);

                int xb1=cx1+r,yb1=cy1-2.0*r/5, wb1=3.0*r/5, hb1 = r/3.0, a1=0,power1=50;
                Rectangle barrel1 (xb1, yb1, wb1, hb1);
                int cx2=800;
                int cy2 = ly[cx2]-r;
                Circle tank2 (cx2, cy2, r);
                tank2.setFill(true);

                Rectangle Rchooseweapon(100,50,100,40);
                Rchooseweapon.setColor(COLOR(238,0,0));
                Rchooseweapon.setFill();
                Rectangle Rfire(450,500,100,50);
                Rfire.setColor(COLOR(238,0,0));
                Rfire.setFill();
                Text Tfire(450,500,"FIRE");


                Rectangle Rotatebarrel(250,50,100,40);
                Rectangle Rpower(400,50,100,40);
                Rectangle Rmove(550,50,100,40);
                Rectangle Rcheckscore(700,50,100,40);
                Rotatebarrel.setColor(COLOR(238,0,0));
                Rotatebarrel.setFill();
                Rpower.setColor(COLOR(238,0,0));
                Rpower.setFill();
                Rmove.setColor(COLOR(238,0,0));
                Rmove.setFill();
                Rcheckscore.setColor(COLOR(238,0,0));
                Rcheckscore.setFill();

                int xb2=cx2-r,yb2=cy2-2.0*r/5, a2=0, wb2 =3.0*r/5, hb2 = r/3.0,power2=50;
                Rectangle barrel2 (xb2, yb2, 3.0*r/5, r/3.0);

                Text choose (100,50, "Choose Weapon");
                Text choose1 (250,50, "Change Angle");
                Text choose2 (400,50, "Change Power");
                Text choose3 (550,50, "Move Tank");
                Text choose4 (700,50, "Check Score" );

                bool turn = 0;
                int w=0;

                int score1=0, score2=0;

                while (1){
                    Rchooseweapon.show();
                    Rotatebarrel.show();
                    Rpower.show();
                    Rmove.show();
                    Rcheckscore.show();
                    choose.show();
                    choose1.show();
                    choose2.show();
                    choose3.show();
                    choose4.show();
                    Tfire.show();

                    int clickval=getClick();


                    if(!turn){
                        if(clickval/65536<=500&&clickval/65536>400&&clickval%65536>=475&&clickval%65536<=525){
                            Rchooseweapon.hide();
                            Rotatebarrel.hide();
                            Rpower.hide();
                            Rmove.hide();
                            Rcheckscore.hide();
                            choose.hide();
                            choose1.hide();
                            choose2.hide();
                            choose3.hide();
                            choose4.hide();
                            Tfire.hide();

                            if (w==1 || w==2 || w==3 || w==6 || w==7)
                                singleshot1(xb1, yb1, xb2, yb2 , wb1, a1, cx2, cy2, cx1, cy1, r, ly, l, tank1, tank2, barrel1, barrel2, score1, w, power1);


                            if (w==4)
                                laser1(xb1, yb1, xb2, yb2 , wb1, a1, cx2, cy2, cx1, cy1, r, ly, l, tank1, tank2, barrel1, barrel2, score1, w, power1);

                            if (w==5)
                                threeshot1(xb1, yb1, xb2, yb2 , wb1, a1, cx2, cy2, cx1, cy1, r, ly, l, tank1, tank2, barrel1, barrel2, score1, w, power1);

                            turn = 1;
                            continue;
                            }

                        if(clickval/65536>=650&&clickval/65536<750&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle RP1(675,90,50,20);
                            Rectangle RP2(725,90,50,20);
                            RP1.setColor(COLOR(238,0,0));
                            RP1.setFill();
                            RP2.setColor(COLOR(238,0,0));
                            RP2.setFill();
                            Text TP1(675,90,"P1");
                            Text TP2(725,90,"P2");
                            int clickvalmove1=getClick();
                            if(clickvalmove1/65536>=650&&clickvalmove1/65536<700&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110){
                                char P1;
                                P1=score1;
                                Text ScoreP1(700,130,P1);
                                wait(2);

                                ScoreP1.hide();
                                RP1.hide();
                                RP2.hide();
                                TP1.hide();
                                TP2.hide();
                                }

                            if(clickvalmove1/65536>=700&&clickvalmove1/65536<750&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110){
                                char P2;
                                P2=score2;
                                Text ScoreP2(700,130,P2);
                                wait(2);
                                ScoreP2.hide();
                                RP1.hide();
                                RP2.hide();
                                TP1.hide();
                                TP2.hide();
                                }
                            }


                        if (clickval/65536<=600&&clickval/65536>500&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle Rright(525,90,50,20);
                            Rectangle Rleft(575,90,50,20);
                            Rright.setColor(COLOR(238,0,0));
                            Rright.setFill();
                            Rleft.setColor(COLOR(238,0,0));
                            Rleft.setFill();
                            Text TRight(525,90,"Right");
                            Text TLeft(575,90,"Left");
                            int clickvalmove = getClick();
                            if(clickvalmove/65536<=550&&clickvalmove/65536>500&&clickvalmove%65536>=70&&clickvalmove%65536<=110)
                                moveTank1('M', cx1, cy1, ly, xb1, yb1, r, tank1, barrel1, a1);
                            if (clickvalmove/65536<=600&&clickvalmove/65536>550&&clickvalmove%65536>=70&&clickvalmove%65536<=110)
                                moveTank1('K', cx1, cy1, ly, xb1, yb1, r, tank1, barrel1, a1);
                            Rright.hide();
                            Rleft.hide();
                            TRight.hide();
                            TLeft.hide();
                            }

                        if (clickval/65536<=750&&clickval/65536>650&&clickval%65536>=30&&clickval%65536<=70)
                            cout << "player1 = " << score1 << endl << "player 2 = " << score2 << endl;

                        if (clickval/65536<=300&&clickval/65536>200&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle Rup(225,90,50,20);
                            Rectangle Rdown(275,90,50,20);
                            Rup.setColor(COLOR(238,0,0));
                            Rup.setFill();
                            Rdown.setColor(COLOR(238,0,0));
                            Rdown.setFill();
                            Text Tup(225,90,"Up");
                            Text Tdown(275,90,"Down");
                            int clickvalmove1 = getClick();
                            if (clickvalmove1/65536<=250&&clickvalmove1/65536>200&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110)
                                moveTank1('H', cx1, cy1, ly, xb1, yb1, r, tank1, barrel1, a1);
                            if (clickvalmove1/65536<=300&&clickvalmove1/65536>250&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110)
                                moveTank1('P', cx1, cy1, ly, xb1, yb1, r, tank1, barrel1, a1);
                            Rup.hide();
                            Rdown.hide();
                            Tup.hide();
                            Tdown.hide();
                            }

                        if (clickval/65536<=150&&clickval/65536>50&&clickval%65536>=30&&clickval%65536<=70){
                            Text laser (450, 150, "Single Shot");
                            Rectangle rlaser (450, 150, textWidth(" Single Shot "), textHeight());
                            Text sshot (450, 170, "Sniper");
                            Rectangle rsshot (450, 170, textWidth("  Sniper"), textHeight());
                            Text tshot (450, 190, "Cruiser");
                            Rectangle rtshot (450, 190, textWidth("  Cruiser"), textHeight());

                            Text cruiser (450, 210, "Ground Digger");
                            Rectangle rcruiser (450, 210, textWidth("  Ground Digger"), textHeight());

                            Text scshot (450, 230, "3-shot");
                            Rectangle rscshot (450, 230, textWidth("  3-shot"), textHeight());

                            Text dball (450, 250, "Homing Missile");
                            Rectangle  rdball (450, 250, textWidth("  Homing Missile"), textHeight());

                            Text weapon (450, 270, "Scatter Shot");
                            Rectangle rweapon (450, 270, textWidth("  Scatter Shot"), textHeight());

                            int clickval1=getClick();

                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>142&&clickval1%65536<=156)
                                w=1;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>162&&clickval1%65536<=176)
                                w=2;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>182&&clickval1%65536<=196)
                                w=3;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>202&&clickval1%65536<=216)
                                w=4;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>222&&clickval1%65536<=236)
                                w=5;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>242&&clickval1%65536<=256)
                                w=6;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>262&&clickval1%65536<=276)
                                w=7;

                            cout << w << endl;
                            }

                        if (clickval/65536<=450&&clickval/65536>350&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle Rinc(375,90,50,20);
                            Rectangle Rdec(425,90,50,20);
                            Rinc.setColor(COLOR(238,0,0));
                            Rinc.setFill();
                            Rdec.setColor(COLOR(238,0,0));
                            Rdec.setFill();
                            Text Tinc(375,90,"Inc");
                            Text Tdec(425,90,"Dec");
                            Rectangle Power(400,130,50,20);
                            char a;
                            Text *TPower[100];
                            Rectangle Rback(400,170,30,20);
                            Text Tback(400,170,"Back");
                            for (int i=0; i<100; i++){
                                a=i;
                                TPower[i] = new Text (400,130,a);
                                TPower[i]->hide();
                                }

                            while (1){
                                TPower[power1]->show();
                                int clickvalmove2 = getClick();
                                if (clickvalmove2/65536<=400&&clickvalmove2/65536>350&&clickvalmove2%65536>=80&&clickvalmove2%65536<=100){
                                    TPower[power1]->hide();
                                    power1++;
                                    TPower[power1]->show();
                                    }
                                else if (clickvalmove2/65536<=450&&clickvalmove2/65536>400&&clickvalmove2%65536>=80&&clickvalmove2%65536<=100){
                                    TPower[power1]->hide();
                                    power1=power1-1;
                                    TPower[power1]->show();
                                    }
                                else if(clickvalmove2/65536<=415&&clickvalmove2/65536>385&&clickvalmove2%65536>=160&&clickvalmove2%65536<=180){
                                    TPower[power1]->hide();
                                    break;
                                    }
                                }

                            Rdec.hide();
                            Tinc.hide();
                            Tdec.hide();
                            Power.hide();
                            Rback.hide();
                            Tback.hide();
                            }

                        }

                    if(turn){
                        clickval = getClick();
                        if(clickval/65536<=500&&clickval/65536>400&&clickval%65536>=475&&clickval%65536<=525){
                            Rchooseweapon.hide();
                            Rotatebarrel.hide();
                            Rpower.hide();
                            Rmove.hide();
                            Rcheckscore.hide();
                            choose.hide();
                            choose1.hide();
                            choose2.hide();
                            choose3.hide();
                            choose4.hide();


                            if (w==1 || w==2 || w==3 || w==6 || w==7)
                            singleshot2(xb2, yb2, xb1, yb1 , wb2, a2, cx1, cy1, cx2, cy2, r, ly, l, tank2, tank1, barrel2, barrel1, score2, w, power2);

                            if (w==4)
                            laser2(xb2, yb2, xb1, yb1 , wb2, a2, cx1, cy1, cx2, cy2, r, ly, l, tank2, tank1, barrel2, barrel1, score2, w, power2);

                            if (w==5)
                            threeshot2(xb2, yb2, xb1, yb1 , wb2, a2, cx1, cy1, cx2, cy2, r, ly, l, tank2, tank1, barrel2, barrel1, score2, w, power2);

                            turn = 0;
                            continue;
                            }

                        if(clickval/65536>=650&&clickval/65536<750&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle RP1(675,90,50,20);
                            Rectangle RP2(725,90,50,20);
                            RP1.setColor(COLOR(238,0,0));
                            RP1.setFill();
                            RP2.setColor(COLOR(238,0,0));
                            RP2.setFill();
                            Text TP1(675,90,"P1");
                            Text TP2(725,90,"P2");
                            int clickvalmove1=getClick();
                            if(clickvalmove1/65536>=650&&clickvalmove1/65536<700&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110){
                                char P1;
                                P1=score1;
                                Text ScoreP1(700,130,P1);
                                wait(2);
                                ScoreP1.hide();
                                RP1.hide();
                                RP2.hide();
                                TP1.hide();
                                TP2.hide();
                                }


                            if(clickvalmove1/65536>=700&&clickvalmove1/65536<750&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110){
                                char P2;
                                P2=score2;
                                Text ScoreP2(700,130,P2);
                                wait(2);
                                ScoreP2.hide();
                                RP1.hide();
                                RP2.hide();
                                TP1.hide();
                                TP2.hide();

                                }
                            }

                        if (clickval/65536<=600&&clickval/65536>500&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle Rright(525,90,50,20);
                            Rectangle Rleft(575,90,50,20);
                            Rright.setColor(COLOR(238,0,0));
                            Rright.setFill();
                            Rleft.setColor(COLOR(238,0,0));
                            Rleft.setFill();
                            Text TRight(525,90,"Right");
                            Text TLeft(575,90,"Left");
                            int clickvalmove = getClick();
                            if(clickvalmove/65536<=550&&clickvalmove/65536>500&&clickvalmove%65536>=70&&clickvalmove%65536<=110)
                                moveTank2('M', cx2, cy2, ly, xb2, yb2, r, tank2, barrel2, a2);
                            if (clickvalmove/65536<=600&&clickvalmove/65536>550&&clickvalmove%65536>=70&&clickvalmove%65536<=110)
                                moveTank2('K', cx2, cy2, ly, xb2, yb2, r, tank2, barrel2, a2);
                            Rright.hide();
                            Rleft.hide();
                            TRight.hide();
                            TLeft.hide();
                        }

                        if (clickval/65536<=750&&clickval/65536>650&&clickval%65536>=30&&clickval%65536<=70)
                            cout << "player1 = " << score1 << endl << "player 2 = " << score2 << endl;

                        if (clickval/65536<=300&&clickval/65536>200&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle Rup(225,90,50,20);
                            Rectangle Rdown(275,90,50,20);
                            Rup.setColor(COLOR(238,0,0));
                            Rup.setFill();
                            Rdown.setColor(COLOR(238,0,0));
                            Rdown.setFill();
                            Text Tup(225,90,"up");
                            Text Tdown(275,90,"down");
                            int clickvalmove1 = getClick();
                            if (clickvalmove1/65536<=250&&clickvalmove1/65536>200&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110)
                                moveTank2('H', cx2, cy2, ly, xb2, yb2, r, tank2, barrel2, a2);
                            if (clickvalmove1/65536<=300&&clickvalmove1/65536>250&&clickvalmove1%65536>=70&&clickvalmove1%65536<=110)
                                moveTank2('P', cx2, cy2, ly, xb2, yb2, r, tank2, barrel2, a2);
                            Rup.hide();
                            Rdown.hide();
                            Tup.hide();
                            Tdown.hide();
                            }

                        if (clickval/65536<=150&&clickval/65536>50&&clickval%65536>=30&&clickval%65536<=70){
                            Text laser (450, 150, "Single Shot");
                            Rectangle rlaser (450, 150, textWidth("  Single Shot"), textHeight());

                            Text sshot (450, 170, "Sniper");
                            Rectangle rsshot (450, 170, textWidth("  Sniper"), textHeight());

                            Text tshot (450, 190, "Cruiser");
                            Rectangle rtshot (450, 190, textWidth("  Cruiser"), textHeight());

                            Text cruiser (450, 210, "Ground Digger");
                            Rectangle rcruiser (450, 210, textWidth("  Ground Digger"), textHeight());

                            Text scshot (450, 230, "3-shot");
                            Rectangle rscshot (450, 230, textWidth("  3-shot"), textHeight());

                            Text dball (450, 250, "Homing Missile");
                            Rectangle  rdball (450, 250, textWidth("  Homing Missile"), textHeight());

                            Text weapon (450, 270, "Scatter Shot");
                            Rectangle rweapon (450, 270, textWidth("  Scatter Shot"), textHeight());
                            int clickval1=getClick();

                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>142&&clickval1%65536<=156)
                                w=1;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>162&&clickval1%65536<=176)
                                w=2;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>182&&clickval1%65536<=196)
                                w=3;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>202&&clickval1%65536<=216)
                                w=4;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>222&&clickval1%65536<=236)
                                w=5;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>242&&clickval1%65536<=256)
                                w=6;
                            if (clickval1/65536>=410&&clickval1/65536<490&&clickval1%65536>262&&clickval1%65536<=276)
                                w=7;

                            cout << w << endl;

                            }
                        if (clickval/65536<=450&&clickval/65536>350&&clickval%65536>=30&&clickval%65536<=70){
                            Rectangle Rinc(375,90,50,20);
                            Rectangle Rdec(425,90,50,20);
                            Rinc.setColor(COLOR(238,0,0));
                            Rinc.setFill();

                            Rdec.setColor(COLOR(238,0,0));
                            Rdec.setFill();
                            Text Tinc(375,90,"Inc");
                            Text Tdec(425,90,"Dec");
                            Rectangle Power(400,130,50,20);
                            char a;
                            Text *TPower[100];
                            Rectangle Rback(400,170,30,20);
                            Text Tback(400,170,"Back");
                            for (int i=0; i<100; i++){a=i;
                            TPower[i] = new Text (400,150,a);
                            TPower[i]->hide();}

                            int clickvalmove2 = getClick();
                            while (1){
                                TPower[power2]->show();
                                int clickvalmove2 = getClick();
                                if (clickvalmove2/65536<=400&&clickvalmove2/65536>350&&clickvalmove2%65536>=80&&clickvalmove2%65536<=100){
                                    TPower[power2]->hide();
                                    power2++;
                                    TPower[power2]->show();
                                    }
                                else if (clickvalmove2/65536<=450&&clickvalmove2/65536>400&&clickvalmove2%65536>=80&&clickvalmove2%65536<=100){
                                    TPower[power2]->hide();
                                    power2=power2-1;
                                    TPower[power2]->show();
                                    }
                                else if(clickvalmove2/65536<=415&&clickvalmove2/65536>385&&clickvalmove2%65536>=160&&clickvalmove2%65536<=180){
                                    TPower[power2]->hide();
                                    break;
                                    }
                                }

                            Rinc.hide();
                            Rdec.hide();
                            Tinc.hide();
                            Tdec.hide();
                            Power.hide();
                            Rback.hide();
                            Tback.hide();
                            }

                        }

                    }

                }


            if(cx>320&&cx<590&&cy>320&&cy<410){                         //This if statement gives Instructions.
                playgame.hide();
                instr.hide();
                exits.hide();
                bkgrd.hide();
                play.hide();
                inst.hide();
                exit.hide();
                Rectangle bkgrud(450,350,900,700);
                bkgrud.setFill();
                bkgrud.setColor(COLOR(127 ,    255 ,    0));
                Rectangle back1(200,100,100,70);
                back1.setFill();
                back1.setColor(COLOR(238,0,0));
                Text bk1(200,100,"< BACK");
                Text instructions1(400,200,"1.Use the left and right arrow keys to move the tank");
                Text instructions2(400,250,"2.Use the up and down arrow keys for adjusting the angle");
                Text instructions3(400,300,"3.Select weapons accordingly");
                Text instructions4(400,350,"4.Adjust power to your wish");
                int clickPos=getClick();
                int hx=clickPos/65536;
                int hy=clickPos%65536;
                cout<<hx<<"  "<<hy;
                if(hx>120&&hx<250&&hy>70&&hy<140){
                    bkgrud.hide();
                    back1.hide();
                    bk1.hide();
                    instructions1.hide();
                    instructions2.hide();
                    instructions3.hide();
                    instructions4.hide();
                    playgame.show();
                    instr.show();
                    exits.show();
                    bkgrd.show();
                    play.show();
                    inst.show();
                    exit.show();
                    }
                }




            if(cx>330&&cx<550&&cy>500&&cy<595){                             //This if statement helps to exit the game.
                break;

                }
            }
        }



    wait(5);

}
