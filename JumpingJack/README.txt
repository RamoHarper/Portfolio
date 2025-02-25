Jumping Jack is a platformer inspired by Jump King

We decided to use assets that were inspired in Mario and Donkey Kong additionally this is a 3D game, we made adjustments so it seems like 2D.

This project uses an engine that we build throughout our degree the engine handles graphics using the Vulkan API the models that we import 
into the game were generated in Blender and exported into the tinygltf format. 

The game also uses ENTT as the framework which is a entity component system for C++ that facilitates how we build the game logic.

During this project my role was to handle all the game states like GameIntro,Pause,GameOver,Game Won. 

In this project I designed and implemented the UI elements specific to each Game State I also implemented the logic for each of them. 
I also had a major role in making sure the camera followed the player across levels which was an issue we had during development. 

In another .txt file you will find a link to a video of our project. 


Here You will find a brief code snippet I worked on. 

As I mentioned one of the issues we were having as a team was the camera not updating when the levels were loaded/unloaded after the player reached the goals. On the Load next level area I updated the camera information so that the camera followed the player across levels. This was code I did not work on originally but I familiarized myself with the codebase of one of my teammates in order to make sure this was adjusted properly. In order to do this I reviewed their commits and their comments to facilitate locating the issue.

if (registry.any_of<GAME::Player>(*i) && registry.any_of<GAME::Goal>(*j))
                    {
                        auto& musicPlayer = registry.get<GAME::musicPlayer>(registry.view<GAME::audioPlayer>().front());
                        std::shared_ptr<const GameConfig> config = registry.get<UTIL::Config>(registry.view<UTIL::Config>().front()).gameConfig;

                        int& level = registry.get<GAME::GameManager>(registry.view<GAME::GameManager>().front()).currentLevel; //Update current level to be the next level.
                        level++;

                        registry.emplace<GAME::Loading>(registry.view<GAME::GameManager>().front()); //add a loading tag to the game manager 
                        if (level > (*config).at("LevelData").at("levels").as<int>()) // if last level was cleared, do something
                        {
                            musicPlayer.gWinJingle.Play();
                            auto gameManager = registry.view<GAME::GameManager>().front(); //get the gamemanager entity
                            registry.emplace<GAME::GameOver>(gameManager, GAME::GameOver{}); //emplace the game over tag on it
                            std::cout << "YOU WIN, GOOD JOB!\n";
                        }
                        else //load the next level
                        {
                            musicPlayer.gWinJingle.Play();
                            auto displayEntity = registry.view<DRAW::CPULevel>().front();
                            registry.patch<DRAW::CPULevel>(displayEntity); //unload level
                            auto playerEntity = registry.view<GAME::Player>().front();
                            auto cameraTargetView = registry.view<DRAW::CameraTarget>();
                            if (!cameraTargetView.empty())
                            {
                                auto cameraTargetEntity = cameraTargetView.front(); 
                                auto& cameraTarget = registry.get<DRAW::CameraTarget>(cameraTargetEntity); 
                                cameraTarget.targetEntity = playerEntity;
                                cameraTarget.heightOffset = 5.0f; 
                                cameraTarget.distanceOffset = 42.0f; 
                            }
                        }

                        break;
                    }